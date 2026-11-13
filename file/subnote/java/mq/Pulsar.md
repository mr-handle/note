# Pulsar

- github：<https://github.com/apache/pulsar>

## 安装pulsar

- 下载压缩包运行

```sh
cd your-pulsar-home/bin

# 保留小黑窗不要动
./pulsar standalone
```

- 测试

```sh
# 1.打开两个虚拟机终端（如果是docker版，则是进入容器交互式终端），都进入your-pulsar-home/bin目录
# 2.在一个终端中执行消费者命令，消费者会监听，所以先执行
./pulsar-client consume topic-test -s 'sub-test'
# 3.在另一个终端中执行生产者命令
./pulsar-client produce topic-test --messages 'hello pulsar'
# 4.查看日志生产者是否成功产生消息，消费者是否成功接收消息
```

## 安装pulsar-manager

### 数据库配置（可选，数据量大时使用）

- 创建数据库和表：[postgresql-schema.sql](/file/pulsar-manager/postgresql-schema.sql)

- 修改配置文件:[application.properties](/file/pulsar-manager/application.properties)

```properties
# 默认内置数据库的连接串要注释掉，url直接写postgresql数据库容器名（不写端口也可以）
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://postgres01:5432/pulsar_manager
spring.datasource.username=postgres
spring.datasource.password=postgres123
```

### compose.yaml

```yaml
pulsar-manager:
    container_name: pulsar-manager
    image: apachepulsar/pulsar-manager:v0.4.0
    ports:
        # 前端端口
        - "9527:9527"
        # 后端端口
        - "7750:7750"
    volumes:
        - /handle/data/pulsar-manager/application.properties:/pulsar-manager/pulsar-manager/application.properties
    environment:
        # 配置文件必须要指定
        - SPRING_CONFIGURATION_FILE=/pulsar-manager/pulsar-manager/application.properties
    # 通过容器服务名和pulsar通信，不写也可以
    # links:
    #     - pulsar
    networks: 
        - my-docker-net
    restart: always
```

### 初始化登录的超级用户密码

```sh
# 第一步
CSRF_TOKEN=$(curl http://localhost:7750/pulsar-manager/csrf-token)
# 第二步
curl \
   -H 'X-XSRF-TOKEN: $CSRF_TOKEN' \
   -H 'Cookie: XSRF-TOKEN=$CSRF_TOKEN;' \
   -H "Content-Type: application/json" \
   -X PUT http://localhost:7750/pulsar-manager/users/superuser \
   -d '{"name": "pulsar", "password": "pulsar123", "description": "test", "email": "username@test.org"}'
```

## Function

- 修改pulsar-home/conf/broker.conf

```conf
functionsWorkerEnabled=true
```

## 进入管理页面

- 访问：<http://localhost:9527/>

- 新增环境：![pulsar-manager-environment](/images/pulsar-manager-environment.png)

## java client

- 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-pulsar</artifactId>
</dependency>
```

- 发送和接收消息

```java
PulsarClient client = PulsarClient.builder()
                                    .serviceUrl("pulsar://localhost:6650")
                                    .build();
// 创建生产者并指定消息类型（默认字节数组）、主题
Producer<String> producer = client.newProducer(Schema.STRING)
                                    .topic("topic-test")
                                    .create();
producer.send("hello pulsar");
producer.close();

TimeUnit.SECONDS.sleep(2);

// 创建消息监听器
MessageListener messageListener = (consumer, message) -> {
    try {
        System.out.println("receive message: " + new String(message.getData()));

        // 消费确认
        consumer.acknowledge(message);
    } catch (Exception e) {
        // 消息处理失败，稍后重新发送
        consumer.negativeAcknowledge(message);
        throw new RuntimeException(e);
    }
};

// 创建消费者并指定主题、订阅
Consumer consumer = client.newConsumer()
                            .topic("topic-test")
                            .subscriptionName("subscription-test")
                            .messageListener(messageListener)
                            .subscribe();
TimeUnit.SECONDS.sleep(2);
consumer.close();
client.close();
```

- spring boot方式发送和接收消息

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public ApplicationRunner runner(PulsarTemplate<String> pulsarTemplate) {
        return (args) -> pulsarTemplate.send("hello-pulsar-topic", "Hello Pulsar World!");
    }

    @PulsarListener(subscriptionName = "hello-pulsar-sub", topics = "hello-pulsar-topic")
    public void listen(String message) {
        System.out.println("Message Received: " + message);
    }
}
```

- jdk17以上版本，需要添加jvm参数

```sh
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/sun.net.util=ALL-UNNAMED
--add-opens java.base/sun.net=ALL-UNNAMED
```

## 事务支持

- 修改broker.conf

```conf
# 开启事务支持
transactionCoordinatorEnabled=true
#开启批量确认
acknowledgmentAtBatchIndexLevelEnable=true
```

- 初始化事务协调器元数据，然后重启bookie和broker

```sh
bin/pulsar initialize-transaction-coordinator-metadata -cs addr1:port1,addr2:port2 -c pulsar-cluster
bin/pulsar-daemon stop broker
bin/pulsar-daemon start broker
bin/pulsar-daemon stop bookie
bin/pulsar-daemon start bookie
```

- 构建支持事务的客户端

```java
PulsarClient client = PulsarClient.builder()
                                    .serviceUrl("pulsar://localhost:6650")
                                    .enableTransaction(true)
                                    .build();

```
