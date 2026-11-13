# RabbitMQ

## 安装RabbitMQ

```sh
rabbitmq:
    container_name: rabbitmq01
    image: rabbitmq:4.0.2-management
    ports:
        # 前端端口
        - "15672:15672"
        # 后端端口
        - "5672:5672"
    environment:
        - RABBITMQ_DEFAULT_USER=rabbitmq
        - RABBITMQ_DEFAULT_PASS=rabbitmq123
    networks: 
        - my-docker-net
    restart: always
```

## 登录rabbitmq管理页面

- 管理页面：主机地址:15672

## spring boot发送/接收测试

- 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

- 配置

```yaml
spring:
    rabbitmq:
        host: localhost
        port: 5672
        username: rabbitmq
        password: rabbitmq123
        # 交换机确认
        publisher-confirm-type: CORRELATED
        # 队列确认
        publisher-returns: true
        listener:
            simple:
                # 消息确认模式设置为手动确认
                acknowledge-mode: manual
                # 每次从队列中取回消息的数量，用来做消费端限流
                prefetch: 1
```

- 监听并消费消息

```java
import com.rabbitmq.client.Channel;
import org.springframework.amqp.core.Message;

@Slf4j
@Component
public class RabbitmqMessageListener {
    private static final String EXCHANGE_DIRECT = "exchange.direct.order";

    private static final String ROUTING_KEY = "order";

    private static final String QUEUE_NAME = "queue.order";

    // 监听+创建交换机、队列和绑定路由键
    @RabbitListener(bindings = @QueueBinding(
            // 持久化设置为true
            value = @Queue(value = QUEUE_NAME, durable = "true"),
            exchange = @Exchange(value = EXCHANGE_DIRECT),
            key = {ROUTING_KEY}
    ))
    // 只监听（如果rabbitmq服务器已经有对应交换机和队列了）
    // @RabbitListener(queues = {QUEUE_NAME})
    public void processMessage(String content, Message message, Channel channel) {
        log.info("receive message: {}", content);
    }
}
```

- 发送消息

```java
@SpringBootTest
public class ApplicationTest {
    private static final String EXCHANGE_DIRECT = "exchange.direct.order";

    private static final String ROUTING_KEY = "order";

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void test() {
        rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq");
    }
}
```

## rabbitmq四种模式

- 1.direct，点对点，只发送到路由键完全匹配的队列
- 2.fanout，广播，不管路由键是什么都全部发送到绑定的队列
- 3.topic，根据路由键匹配规则匹配，*匹配一个，#匹配0个或多个，只发送到匹配的队列
- 4.headers，很少用

## 定义默认消息转换器

```java
package com.handle.amqp.configuration;

import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置amqp默认的消息转换器，将对象转成json发送
 *
 * @author handle
 * @date 2022-05-06 22:24:30
 * @since jdk-1.8
 */
@Configuration
public class AmqpConfiguration {
    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}

```

## 创建、绑定或删除交换器、队列

```java
@SpringBootTest
public class AmqpApplicationTests {
    @Autowired
    private AmqpAdmin amqpAdmin;

    @Test
    public void testAmqpAdmin() {
        // 创建交换器
        amqpAdmin.declareExchange(new DirectExchange("amqpAdmin.direct"));

        // 创建队列
        amqpAdmin.declareQueue(new Queue("amqpAdmin.queue"));

        // 绑定队列到交换器
        amqpAdmin.declareBinding(
            new Binding("amqpAdmin.queue", Binding.DestinationType.QUEUE, "amqpAdmin.direct", "amqpAdmin", null));
        // 删除队列
        amqpAdmin.deleteQueue("amqpAdmin.queue");
        // 删除交换器
        amqpAdmin.deleteExchange("amqpAdmin.direct");
    }
}
```

## 消息可靠

### 生产端

## 消息确认方式1

- 最常用方式

```java
@Slf4j
@Configuration
public class RabbitmqConfiguration implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnsCallback {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @PostConstruct
    public void initRabbitTemplate() {
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnsCallback(this);
    }
    /**
     * 确认消息是否发送到交换机（成功/失败）
     * ack true，成功发送到交换机
     * cause 发送失败原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        if (ack) {
            return;
        }
        log.error("消息发送到交换机失败，原因：{}", cause);
        // 补偿措施
    }

    /**
     * 确认消息是否发送到队列，失败时才调用
     */
    @Override
    public void returnedMessage(ReturnedMessage returnedMessage) {
        log.error("消息发送到队列失败");
        log.info("消息主体：{}", new String(returnedMessage.getMessage().getBody()));
        log.info("应答码：{}", returnedMessage.getReplyCode());
        log.info("应答描述：{}", returnedMessage.getReplyText());
        log.info("交换器：{}", returnedMessage.getExchange());
        log.info("路由键：{}", returnedMessage.getRoutingKey());
    }
}
```

## 消息确认方式2

- 备份交换机，针对目标交换机故障的情况
    - 创建交换机，类型必须是fanout
    - 创建队列，和备份交换机绑定
    - 给目标交换机（Arguments）指定备份交换机

### 服务器

- 消息持久化
    - 交换机持久化（默认）：durable true，autoDelete false
    - 队列持久化（默认）：durable true，autoDelete false

### 消费端

- 消费消息成功，给服务器返回ack信息，然后消息队列删除该消息
- 消费消息失败，给服务器返回nack信息，然后消息队列把消息恢复为待消费状态，这样消费者可以再次取回消息重试（需要消费端支持幂等性）

## 消息超时

- 可在队列创建的时候设置消息的过期时间（x-message-ttl），队列中的所有消息使用这个超时
时间
- 给具体的某个消息设置过期时间
- 如果这两个都做了设置，哪个时间短，哪个生效

```java
// 给具体的某个消息设置过期时间
MessagePostProcessor messagePostProcessor = message -> {
    message.getMessageProperties().setExpiration("5000");
    return message;
};
rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq", messagePostProcessor);
```

## 死信和死信队列

- 当一个消息无法被消费，它就变成了死信
- 死信产生的原因有如下三种
    - 消费者拒接消息（basicNack/basicReject），并且requeue=false
    - 队列中消息数量达到限制（x-max-length），如果再来一条消息，根据先进先出原则，队列中最早的消息会变成死信
    - 消息超时未被消费
- 死信的处理方式
    - 丢弃：不重要的消息直接丢弃，不做处理
    - 入库：把死信写入数据库，日后处理
    - 监听：将消息放到死信队列，专门设置消费端监听死信队列，做后续处理（通常采用）
        - 创建队列时设置x-dead-letter-exchange、x-dead-letter-routing-key

## 延迟队列

- 实现方案
    - 设置消息超时时间+死信队列
    - 安装插件，延迟极限最多两天

### 安装延迟队列插件

官网：<https://github.com/rabbitmq/rabbitmq-delayed-message-exchange>

- 官网下载插件文件放到rabbitmq的指定目录（/plugins）
- 启用插件

```sh
rabbitmq-plugins enable 插件名
```

- 然后重启rabbitmq

- 创建交换机，指定type为x-delayed-message，再通过参数x-delayed-type指定交换机类型（direct/topic等)

- 创建延时消息

```java
MessagePostProcessor messagePostProcessor = message -> {
    // x-delay只有装了上面的延时插件才会生效
    message.getMessageProperties().setHeader("x-delay", "5000");
    return message;
};
rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq", messagePostProcessor);
```

- 需要注意的是，通过延时队列插件发送的消息无论成功/失败都会走returnedMessage方法

## 事务消息

- 只对生产者端生效，控制缓存里面的消息要么全部发送到broker（业务逻辑都OK），要么全部都不发送（业务逻辑异常了）
- 不能解决生产端消息可靠性传递的问题

- 修改配置

```java
@Bean
public RabbitTransactionManager rabbitTransactionManager(CachingConnectionFactory factory) {
    return new RabbitTransactionManager(factory);
}

@Bean
public RabbitTemplate rabbitTemplate2(CachingConnectionFactory factory) {
    RabbitTemplate rabbitTemplate1 = new RabbitTemplate(factory);
    rabbitTemplate1.setChannelTransacted(true);
    return rabbitTemplate1;
}
```

- 创建事务，消息1和消息2要么全部发送要么都不发送

```java
@Transactional
@Test
// junit默认会回滚事务，这里为了测试效果所以要设置为false
@Rollback(value = false)
public void test2() {
    rabbitTemplate1.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "message1");
    int i = 1 /0 ;
    rabbitTemplate1.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "message2");
}
```

## 惰性队列

- 前提是队列是持久化的
- 未设置惰性模式时队列只有在队列满了或者borker关闭的时候才做持久化操作
- 设置了惰性队列时（默认），服务器空闲就会持久化

## 优先级队列

- 创建队列时指定参数x-max-priority，（1-5）就可以了，消息的优先级就不能高于这个设置的值

```java
MessagePostProcessor messagePostProcessor = message -> {
    // 不要超过x-max-priority，数值越高，优先级越高
    message.getMessageProperties().setPriority(3);
    return message;
};
rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq", messagePostProcessor);
```

## rabbitmq集群

### 搭建rabbitmq集群

- 思想：锚定某一个rabbitmq服务器作为基础节点，其它的rabbitmq服务器都加入到这个节点

- 所有节点的Cookie值要设置为一样的

```sh
# 查看cookie
cat /var/lib/rabbitmq/.erlang.cookie
# 设置cookie
vi /var/lib/rabbitmq/.erlang.cookie
```

-修改/etc/hosts，追加如下内容

```sh
具体ip node01
具体ip node02
具体ip node03
```

- 重置节点应用并加入集群

```sh
rabbitmqctl stop_app
rabbitmqctl reset
# 所有rabbitmq服务器节点都加入到node01
rabbitmqctl join_cluster rabbti@node01
rabbitmqctl start_app
```

- 最后可以在管理页面的nodes查看集群

### rabbitmq负载均衡

- 安装haproxy

```sh
# Red Hat系列Linux
yum install -y haproxy
# 查看版本
haproxy -v
```

- 修改配置文件：/etc/haproxy/haproxy.cfg

```conf
# 前端功能
# 前端配置（自定义前端名称）
frontend rabbitmq_ui_frontend
# 以后浏览器通过访问这个地址进入管理页面
bind ip:port
mode http
# 后端配置
# 默认后端，引用后端名称
default_backend rabbitmq_ui_backend
# 后端名称（自定义后端名称）
backend rabbitmq_ui_backend
mode http
balance roundrobin
option httpchk GET /
# 服务器节点的地址
server rabbitmq_ui1 ip1:15672 check
server rabbitmq_ui2 ip2:15672 check
server rabbitmq_ui3 ip3:15672 check

# 核心功能（自定义前端名称）
frontend rabbitmq_frontend
# 以后后端通过访问这个地址进入和rabbitmq通信
bind ip:port
mode tcp
# 后端配置
# 默认后端，引用后端名称
default_backend rabbitmq_backend
# 后端名称（自定义后端名称）
backend rabbitmq_backend
mode tcp
balance roundrobin
# 服务器节点的地址
server rabbitmq_1 ip1:5672 check
server rabbitmq_2 ip2:5672 check
server rabbitmq_3 ip3:5672 check
```

- 设置SELinux策略，允许haproxy拥有权限连接任意接口

```sh
setsebool -P haproxy_connect_any=1
# 启动haproxy
systemctl start haproxy
# 开机启动
systemctl enable haproxy
```

### 仲裁队列

- 在某一个节点上创建队列，会自动分散到各个节点上
- 创建队列时，type选择Quorum，node选择一个节点作为主节点
