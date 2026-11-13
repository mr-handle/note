# Spring Cloud

## Spring Cloud 组件

- 1.服务注册与发现，Etcd、Consul、Nacos

- 2.服务调用，LoadBalancer、OpenFeign

- 3.分布式事务，LCN、Hmily、Seata

- 4.服务熔断和降级，resilience4j、sentinel

- 5.服务链路追踪，Micrometer Tracing

- 6.服务网关：Gateway

- 7.服务配置：Consul、Nacos

## CAP

- C，一致性
- A，高可用
- P，分区容错性

## Eureka

### Eureka服务器

- maven dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

- 应用配置

```properties
server.port=7001

# eureka服务器的实例名称
eureka.instance.hostname=eureka7001.com

# 是否注册到eureka注册中心
eureka.client.register-with-eureka=false

# false表示自己就是注册中心，自己的职责就是维护服务实例，并不需要去检索服务
eureka.client.fetch-registry=false

# 与eureka服务器交互的地址查询服务和注册服务都需要依赖这个地址
# 单机版配置
# eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka

eureka.client.service-url.defaultZone=http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka

# 关闭Eureka自我保护，使得不可用服务及时被剔除
eureka.server.enable-self-preservation=false

# 扫描失效服务的间隔时间（毫秒）
eureka.server.eviction-interval-timer-in-ms=3000
```

- 主启动类

```java
@SpringBootApplication
@EnableEurekaServer
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Eureka客户端

- maven dependency

```xmln
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

- 应用配置

```properties
server.port=8001

# 作为注册进Eureka服务器的应用名称
spring.application.name=cloud-payment-service

# Eureka客户端配置

# 定义服务实例名称
eureka.instance.instance-id=payment8001

# 访问路径可以显示ip地址
eureka.instance.prefer-ip-address=true

# 是否注册到eureka服务器
eureka.client.register-with-eureka=true

# 是否从eureka服务器抓取已有的注册信息，集群必须设置为true才能配合ribbon使用负载均衡
eureka.client.fetch-registry=true

# Eureka服务器地址，即Eureka服务器的eureka.client.service-url.defaultZone

# eureka单机版
# eureka.client.service-url.defaultZone=http://localhost:7001/eureka

# eureka集群版
eureka.client.service-url.defaultZone=http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka

# Eureka客户端向服务器发送心跳的时间间隔（秒）
eureka.instance.lease-renewal-interval-in-seconds=1
# Eureka服务器在收到最后一次心跳后等待的时间上限（秒），超时剔除服务
eureka.instance.lease-expiration-duration-in-seconds=2
```

- 主启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 服务发现

对注册进Eureka注册中心的微服务，可以通过服务发现获取该微服务的信息

- 主启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 控制器类

```java
@Resource
private DiscoveryClient discoveryClient;

/**
* 通过discoveryClient获取注册到Eureka的微服务信息
*/
@GetMapping("/payment/discovery")
public Object discovery() {
    // 获取注册到Eureka的微服务名称列表
    List<String> services = discoveryClient.getServices();
    for (String service : services) {
        log.info("service: {}", service);
    }

    // 通过微服务名称获取微服务的实例
    List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
    for (ServiceInstance instance : instances) {
        log.info("{}", instance.toString());
    }
    return discoveryClient;
}
```

## OpenFeign

### 接口项目

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

- 接口

```java
@FeignClient(name = "account-service", contextId = "account-service1")
public interface AccountApi {
    // 不能在接口上写@RequestMapping了，请求路径要写全（本体handler类上@RequestMapping注解的路径要加上来）
    @GetMapping("/account/getAccount")
    ResultVo<AccountPo> getAccount(@RequestParam("userId") Long userId);
}
```

#### @FeignClient

- 用来定义OpenFeign接口

- name，填接口实现方在注册中心的服务名，如果需要网关路由，则填网关在注册中心的服务名

- contextId，当定义了多个name一样的OpenFeign接口时，用contextId为bean起别名

### 消费项目

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<!-- loadbalancer不加启动会报错 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 配置

```properties
spring.application.name=openFeign-consumer-service
server.port=8080
spring.cloud.nacos.discovery.server-addr=localhost:8848
```

- 主启动类

```java
// 开启OpenFeign功能，并指定openFeign接口所在包
@EnableFeignClients(basePackages = "com.handle.open.feign.api")
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 使用openFeign的接口

```java
@Service
public class AccountServiceImpl implements AccountService{
    @Resource
    private AccountApi accountApi;

    @Override
    public ResultVo<AccountPo> getAccount(Long userId) {
        return accountApi.getAccount(userId);
    }
}
```

### 超时配置

- 全局配置

```properties

# 连接超时
spring.cloud.openfeign.client.config.default.connectTimeout=5000
# 处理超时
spring.cloud.openfeign.client.config.default.readTimeout=5000
```

- 指定配置（会覆盖全局配置）

```properties

# 连接超时
spring.cloud.openfeign.client.config.服务名.connectTimeout=5000
# 处理超时
spring.cloud.openfeign.client.config.服务名.readTimeout=5000
```

### 重试配置

```java
@Configuration
public class OpenFeignConfiguration {
    @Bean
    public Retryer retryer() {
        // 默认Retryer.NEVER_RETRY，这里设置为1000ms后，每隔1s，最多请求3次
        return new Retryer.Default(1000, 1, 3);
    }
}
```

### 使用 Apache HttpClient 5

- 添加依赖

```xml
<!-- 包含了httpclient5 -->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-hc5</artifactId>
</dependency>
```

- 修改配置文件

```properties
spring.cloud.openfeign.httpclient.hc5.enabled=true
```

### 请求/响应压缩

```properties
# 开启GZIP压缩
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.response.enabled=true

# 触发压缩的数据类型
spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json
# 触发压缩的最小值
spring.cloud.openfeign.compression.request.min-request-size=2048
```

### 日志打印

- 配置方式（openfeign日志级别和接口日志级别都要设置）

```properties
# openfeign日志
spring.cloud.openfeign.client.config.default.logger-level=full
# 指定openfeign接口日志等级
logging.level.com.handle.base.api.GatewayAccountApi=debug
```

- IOC方式（还没测试过接口日志等级不写会不会打印）

```java
@Configuration
public class OpenFeignConfiguration {
    @Bean
    public Logger.Level openFeignLoggerLevel() {
        return new Logger.Level.BASIC;
    }
}
```

## BUS 消息总线

- maven dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 应用配置

```properties
# rebbitmq配置
rebbitmq.host=host
rebbitmq.port=5672
rebbitmq.username=guest
rebbitmq.password=guest

# 暴露bus刷新配置的端点
management.endpoints.web.exposure.include=bus-refresh
```

## 链路追踪

### 收集用micrometer

- 依赖

```xml
<dependences>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-tracing</artifactId>
    </dependency>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-tracing-bridge-brave</artifactId>
    </dependency>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-observation</artifactId>
    </dependency>
    <dependency>
        <groupId>io.github.openfeign</groupId>
        <artifactId>feign-micrometer</artifactId>
    </dependency>
    <dependency>
        <groupId>io.zipkin.reporter2</groupId>
        <artifactId>zipkin-reporter-brave</artifactId>
    </dependency>
    <!-- 要依赖actuator的自动配置（配置文件里面management的配置） -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependences>
```

- 配置

```properties
# 数据发给zipkin展示，指定其地址信息，http://不要漏掉
management.zipkin.tracing.endpoint=http://localhost:9411/api/v2/spans
# 采样频率，默认0.1（10次记录一次），值越大收集越及时
management.tracing.sampling.probability:1.0
```

### 展示用zipkin

- 官网：<https://zipkin.io/>

- 安装运行后访问页面：<http://localhost:9411/>

- 需要手动点`RUN QUERY`，刷新页面不会更新数据

- 请求链路追踪：一条链路通过trace id唯一标识， span表示发起的请求信息，各span通过parent id 关联起来

## Spring Cloud Gateway

客户端的请求先通过匹配规则找到合适的路由，就能映射到具体的服务

然后请求经过过滤器处理后转发给具体的服务，服务处理后，再次经过过滤器处理，最后返回给客户端

### 三大核心

- Route，路由，它由id，目标url，一系列断言和过滤器组成，断言为true则匹配该路由，解决找不找得到的问题

- Predicate，断言，匹配条件，用来匹配请求中的所有内容（如请求头、请求参数等），如果请求和断言匹配，则进行路由，解决找到后能不能访问的问题

- Filter，过滤器，GatewayFilter的实例，可在请求被路由前或者之后对请求进行增强

### Route

#### id

```yaml
spring:
    cloud:
        gateway:
            routes:
                -   # 路由id，自定义key
                    id: gateway-test-service-route
```

#### uri

```yaml
spring:
    cloud:
        gateway:
            routes:
                - 
                    # 匹配后提供服务的路由
                    # 服务地址不要写死http://localhost:8080
                    # 要写成lb://服务在注册中心的名称，这样要是服务端口变化了就不用做改动，也可实现负载均衡
                    uri: lb://gateway-test-service
```

### Predicate

#### 官方提供的断言

```yaml
spring:
    cloud:
        gateway:
            routes:
                - 
                    predicates:
                        # predicate格式：predicate名称=值
                        # 使用Path，与此路径相匹配的才进行路由
                        - Path=/gateway/getInfo/**
                        # 时间断言，指定一个Java ZonedDateTime，在这个时间点之前/后/之间才能访问，格式可通过ZonedDateTime.now(ZoneId.systemDefault()).toString()得到
                        - After=2024-10-14T16:42:09.008501600+08:00[Asia/Shanghai]
                        - Before=2099-10-14T16:42:09.008501600+08:00[Asia/Shanghai]
                        - Between=2024-10-14T16:50:09.008501600+08:00[Asia/Shanghai],2099-10-14T16:51:09.008501600+08:00[Asia/Shanghai]
                        # 指定请求头包含某个Cookie（key和value都要匹配）才能访问
                        - Cookie=username,handle
                        # 指定请求头要有某个属性，并且属性值符合某个正则表达式才能访问
                        - Header=X-Request-Id,\d+
                        # 指定请求头的Host值符合某个匹配规则才能访问
                        - Host=**.handle.org,**.handle.com
                        # 指定请求方式（例如GET），符合才能访问
                        - Method=GET
                        # 指定请求参数名，以及参数值符合某个正则表达式才能访问
                        - Query=userId,\d+
                        # IP地址表示方法（CIDR），24表示ip地址前缀的位数，最大不能超过32，约束其前缀必须为192.168.56，最后一个字节是0-255任意，其它IP不允许访问
                        - RemoteAddr=192.168.56.1/24
                        # - Weight=group1, 2
                        # - XForwardedRemoteAddr=192.168.1.1/24
```

#### 自定义断言

- 仿照官方的过滤器实现，命名必须为XxxRoutePredicateFactory

### Filter

#### Global Filters

- 作用于所有路由

```yaml
spring:
    cloud:
        gateway:
            default-filters:
                - PrefixPath=/gateway
```

#### GatewayFilter

- 作用于单一路由，官方提供了大量的GatewayFilter

```yaml
spring:
    cloud:
        gateway:
            routes:
                -
                    id: add_request_header_route
                    uri: https://example.org
                    filters:
                        # 请求头过滤器
                        - AddRequestHeader=X-Request-Red, red
                        # 多个AddRequestHeader另写一行
                        - AddRequestHeader=X-Request-Green, green
                        - SetRequestHeader=X-Request-Green, GREEN
                        - RemoveRequestHeader=X-Request-Yellow

                        # 请求参数过滤器
                        # 如果请求参数里面有red=RED，会覆盖AddRequestParameter设置的red=red
                        - AddRequestParameter=red, red
                        # 前端传red为任何值，后端获取red的值，都为null
                        - RemoveRequestParameter=red

                        # 响应头过滤器
                        - AddResponseHeader=X-Response-Red, red
                        - SetResponseHeader=X-Response-Red, RED
                        - RemoveResponseHeader=X-Response-Yellow

                        # 路径过滤器
                        # 添加路径前缀，加了前缀的请求地址和断言配置的Path路径做比对
                        - PrefixPath=/gateway

                        # 假设断言配置的Path=/abc/{placeholder}，Path除了{placeholder}的内容，其它的内容会被SetPath取代
                        # 比如真实地址是：http:/localhost/gateway/getInfo
                        # 只配置了Path=/abc/{placeholder}和SetPath=/gateway/{placeholder}
                        # 然后请求地址变成：http:/localhost/abc/getInfo依然能够正确访问
                        - SetPath=/gateway/{placeholder}

                        # 符合断言Path配置的请求都跳转到百度
                        - RedirectTo=302, https://www.baidu.com
                        # 符合断言Path配置的请求都跳转到百度，并且带上原来的请求参数
                        - RedirectTo=302, https://acme.org, true

```

#### 自定义Filter

#### 自定义全局Filter

```java
// 加入IOC
@Component
public class ProcessTimeGlobalFilter implements GlobalFilter, Ordered {
    private static final String BEGIN_TIME = "BEGIN_TIME";

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        exchange.getAttributes().put(BEGIN_TIME, Instant.now().toEpochMilli());
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            Long beginTime = exchange.getAttribute(BEGIN_TIME);
            if (Objects.nonNull(beginTime)) {
                String host = exchange.getRequest().getURI().getHost();
                int port = exchange.getRequest().getURI().getPort();
                String path = exchange.getRequest().getURI().getPath();
                String rawQuery = exchange.getRequest().getURI().getRawQuery();
                long processTime = Instant.now().toEpochMilli() - beginTime;
                System.out.println(host + ": " + port + path + "parameter" + rawQuery + " 接口调用时长：" + processTime);
            }
        }));
    }

    // 越小优先级越高
    @Override
    public int getOrder() {
        return 0;
    }
}
```

#### 自定义单一Filter

- 仿照官方的过滤器实现，类名必须是XxxGatewayFilterFactory

### Gateway微服务

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!-- 需要入驻注册中心 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!-- 填写uri:lb://gateway-test-service的时候需要依赖负载均衡 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<!-- 不需要spring-boot-starter-web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 配置

```yaml
server:
    port: 8000
spring:
    application:
        name: gateway-service
    cloud:
        nacos:
            discovery:
                server-addr: localhost:8848
                namespace: b70655ab-8c47-4dbb-b6b0-589f8eda9441
                username: nacos
                password: nacos
        gateway:
            routes:
                -
                    id: gateway-test-service-route
                    uri: lb://gateway-test-service
                    predicates:
                        - Path=/gateway/getInfo/**
```

- 主启动类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### gateway + sentinel

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-simple-http</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-spring-cloud-gateway-adapter</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 配置

```yaml
server:
    port: 8000
spring:
    application:
        name: gateway-sentinel-service
    cloud:
        nacos:
            discovery:
                server-addr: localhost:8848
                namespace: b70655ab-8c47-4dbb-b6b0-589f8eda9441
                username: nacos
                password: nacos
        gateway:
            routes:
                -
                    id: gateway-test-service-route
                    uri: lb://gateway-test-service
                    predicates:
                        - Path=/gateway/getInfo/**
```

- 配置文件

```java
@Configuration
public class GatewayConfiguration {
    private final List<ViewResolver> viewResolvers;

    private final ServerCodecConfigurer serverCodecConfigurer;

    // 构造方法
    public GatewayConfiguration(ObjectProvider<List<ViewResolver>> viewResolversProvider,
            ServerCodecConfigurer serverCodecConfigurer) {
        this.viewResolvers = viewResolversProvider.getIfAvailable(Collections::emptyList);
        this.serverCodecConfigurer = serverCodecConfigurer;
    }

    // 注入SentinelGatewayBlockExceptionHandler
    @Bean
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public SentinelGatewayBlockExceptionHandler sentinelGatewayBlockExceptionHandler() {
        // Register the block exception handler for Spring Cloud Gateway.
        return new SentinelGatewayBlockExceptionHandler(viewResolvers, serverCodecConfigurer);
    }

    // 注入SentinelGatewayFilter
    @Bean
    @Order(-1)
    public GlobalFilter sentinelGatewayFilter() {
        return new SentinelGatewayFilter();
    }


    // 官方代码里面这个注解还是javax.annotation-api的
    // 但是@PostConstruct已经是jakarta.annotation.PostConstruct的了，测试了没问题
    @PostConstruct
    public void init() {
        initGatewayRules();
    }

    // 初始化
    private void initGatewayRules() {
        Set<GatewayFlowRule> rules = new HashSet<>();
        // 定义限流规则，一秒钟QPS超过2个进行限流
        rules.add(new GatewayFlowRule("gateway-test-service-route").setCount(2)
                                                                   .setIntervalSec(1));
        GatewayRuleManager.loadRules(rules);

        // 定义触发限流返回的信息内容
        BlockRequestHandler blockHandler = (serverWebExchange, throwable) -> {
            HashMap<String, Object> map = new HashMap<>();
            map.put("code", HttpStatus.TOO_MANY_REQUESTS.value());
            map.put("message", "触发限流，请不要频繁请求");
            return ServerResponse.status(HttpStatus.TOO_MANY_REQUESTS)
                                 .contentType(MediaType.APPLICATION_JSON)
                                 .body(BodyInserters.fromValue(map));
        };
        GatewayCallbackManager.setBlockHandler(blockHandler);
    }
}
```
