# Spring Cloud Alibaba

- maven dependency

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>{project-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Nacos

- 官网：<https://nacos.io/zh-cn/index.html>

- github：<https://github.com/alibaba/nacos>

### Nacos部署

#### 数据持久化

- 1.创建nacos数据库

```sql
create database if not exists `nacos` default character set utf8mb4 collate utf8mb4_unicode_ci;
```

- 2.执行[nacos-mysql.sql](/sql/nacos-mysql-schema.sql)脚本

- 3.修改conf/application.properties配置

```properties
spring.sql.init.platform=mysql
db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?useUnicode=true&characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&serverTimezone=GMT%2B8
db.user.0=root
db.password.0=mysql123
```

- 4.运行nacos服务

```sh
# 单机启动
sh startup.sh -m standalone

# 集群启动
sh startup.sh
```

- 访问nacos页面：<http://localhost:8848/nacos>

#### nacos集群

- 1）nacos解压缩后复制成n份

- 2）修改cluster.conf文件配置

```properties
# 填 hostname -i 命令显示的ip，如果是同一ip端口不能连号！
192.168.31.149:3333
192.168.31.149:4444
192.168.31.149:5555
```

- 3）分别修改n个nacos的application.properties文件里面的端口号

- 4）分别启动n个nacos服务

### nacos-docker

- compose.yaml

```yaml
nacos:
    container_name: nacos01
    image: nacos/nacos-server:v2.3.0
    ports:
        - "8848:8848"
        - "9848:9848"
    environment:
        - MODE=standalone
        - SPRING_DATASOURCE_PLATFORM=mysql
        - MYSQL_DATABASE_NUM=1
        - MYSQL_SERVICE_HOST=mysql01
        - MYSQL_SERVICE_PORT=3306
        - MYSQL_SERVICE_DB_NAME=nacos
        - MYSQL_SERVICE_USER=root
        - MYSQL_SERVICE_PASSWORD=mysql123
        # 在默认的连接参数后加上allowPublicKeyRetrieval=true，这样就不用手动连接一次mysql才能成功启动nacos
        - MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=GMT%2B8
        # 登录配置
        - NACOS_AUTH_SYSTEM_TYPE=nacos
        - NACOS_AUTH_ENABLE=true
        - NACOS_AUTH_TOKEN=VGhpc0lzTXlDdXN0b21TZWNyZXRLZXkwMTIzNDU2Nzg=
        - NACOS_AUTH_IDENTITY_KEY=nacos
        - NACOS_AUTH_IDENTITY_VALUE=nacos
    volumes:
        - /handle/data/nacos/logs/:/home/nacos/logs
    networks: 
        - my-docker-net
    depends_on:
        - mysql
    restart: always
```

### 注册中心

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 配置

```properties
spring.application.name=nacos-discovery-service
server.port=8080
spring.cloud.nacos.discovery.server-addr=localhost:8848
# nacos注册中心命名空间id
spring.cloud.nacos.discovery.namespace=6643e6b9-6ca9-4d8f-86bd-34fbb893a976
# nacos注册中心分组名称
spring.cloud.nacos.discovery.group=DEV_GROUP
# spring.cloud.nacos.discovery.username=nacos
# spring.cloud.nacos.discovery.password=nacos
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

- 配置负载均衡

注册到Nacos的微服务消费者新建配置类

```java
@Configuration
public class ApplicationConfiguration {
    @LoadBalanced
    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

- （对于虚拟机的NAT和docker版本的nacos）做好9848、9849端口的映射

### 配置中心

- Data ID命名规则：${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 新建文件bootstrap.properties作为全局配置

```properties
server.port=3377

spring.application.name=nacos-config-client

# nacos注册中心地址
spring.cloud.nacos.discovery.server-addr=www.laodeli.top:8848

# nacos注册中心命名空间id
spring.cloud.nacos.discovery.namespace=6643e6b9-6ca9-4d8f-86bd-34fbb893a976

# nacos注册中心分组名称
spring.cloud.nacos.discovery.group=DEV_GROUP

# nacos服务配置中心地址
spring.cloud.nacos.config.server-addr=www.laodeli.top:8848

# 设置配置中心命名空间id
spring.cloud.nacos.config.namespace=6643e6b9-6ca9-4d8f-86bd-34fbb893a976

# 设置配置中心分组名称
spring.cloud.nacos.config.group=DEV_GROUP

# 配置文件后缀
spring.cloud.nacos.config.file-extension=properties
```

- 新建文件application.properties作为应用配置

```properties
# 读配置中心${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}文件
# dev、test、pro
spring.profiles.active=dev
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

- 控制器类

```java
@RestController
// 使当前类下的配置支持动态刷新
@RefreshScope
@RestController
@RequestMapping("/nacos/config")
public class ApplicationController {
    @Value("${application.info}")
    private String info;

    @GetMapping("/getInfo")
    public String getInfo() {
        return info;
    }
}
```

## Sentinel

github:<https://github.com/alibaba/Sentinel/>
<br/>
官网：<https://sentinelguard.io/zh-cn/>

### 控制台

- 下载sentinel-dashboard-1.8.1.jar

- 启动sentinel-dashboard-1.8.1.jar

- 页面：<http://localhost:8080>

- 用户名/密码：sentinel

- 采用的时懒加载，要手动请求一次接口才会在控制台刷新接口信息

### 流控规则

#### 阈值类型

- QPS，每秒请求数

- 并发线程数，涉及线程上下文切换和线程回收，精确度不如QPS

#### 流控模式

- 直接，当资源（接口）的访问达到阈值后，限流自己

- 关联，当与资源A关联的资源B访问达到阈值后，限流资源A

- 链路，来自不同链路的请求对某一个资源进行访问，实施不同的限流措施

#### 流控效果

- 快速失败，直接抛出异常

- Warm Up，预热，使得系统从空闲状态，经过预期时间后到达最大请求阈值，而不是流量突然增大

    - 访问量阈值从阈值除以冷却因子（默认3）的结果值开始，经过预热时长逐渐提升到设定的阈值

    - 主要用于启动需要额外开销的场景，如建立数据库连接等

- 排队等待，访问达到阈值时，过了间隔时间才允许通过下一个请求

    - 主要用于处理间隔性突发的流量：系统某一时刻突然要处理大量请求，接下来的几秒却处于空闲状态的场景

    - 匀速排队模式暂不支持QPS大于1000的场景

### 熔断规则

- 熔断策略
    - 慢调用比例
        - 请求的响应时间>最大RT(最大响应时间)，则统计为慢调用
        - 当单位统计时长内请求数目大于设置的最小请求数，且慢调用比例大于阈值，则接下来的熔断时长内请求会被熔断
        - 经过熔断时长后熔断器会进入半开状态，若接下来的一个请求响应时间小于设置的最大RT则结束熔断，否则再次被熔断
    - 异常比例
        - 当单位统计时长内请求数目大于设置的最小请求数，且异常比例大于阈值，则接下来的熔断时长内请求会被熔断
        - 经过
    - 异常数
        - 当单位统计时长内请求数目大于设置的最小请求数，且异常数大于阈值，则接下来的熔断时长内请求会被熔断
        - 经过熔断时长后熔断器会进入半开状态，若接下来的一个请求正常完成则结束熔断，否则再次被熔断

### 热点规则

- 对请求接口的入参进行监控限流
- 参数例外项，当参数是某一个值时，它的阈值可以达到设置的限流阈值才限流
    - 填写完后记得点击添加按钮

### 授权规则

- 需要先在客户端代码里面实现RequestOriginParser接口

```java
@Component
public class SentinelRequestOriginParser implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest request) {
        return request.getParameter("serverName");
    }
}
```

- handler

```java
@GetMapping("/blackList")
public ResultVo<String> blackList() {
    return ResultVo.success("test blackList");
}
```

- 控制台设置授权规则

![授权规则](/images/授权规则.png)

- 测试
    - url：<http://localhost:8080/sentinel/blackList?serverName=test>
    - 当请求参数serverName=test时，结果：Blocked by Sentinel (flow limiting)

### 规则持久化

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

- 配置

```properties
# 配置规则持久化到nacos的json配置文件，rule1为自定义key，可以当作规则名，可以定义多个，分别配置不同的限流类型
spring.cloud.sentinel.datasource.rule1.nacos.server-addr=localhost:8848
spring.cloud.sentinel.datasource.rule1.nacos.namespace=b70655ab-8c47-4dbb-b6b0-589f8eda9441
spring.cloud.sentinel.datasource.rule1.nacos.data-id=${spring.application.name}
spring.cloud.sentinel.datasource.rule1.nacos.group-id=DEFAULT_GROUP
# 定义规则配置文件为json格式
spring.cloud.sentinel.datasource.rule1.nacos.data-type=json
# 流控规则，参考com.alibaba.cloud.sentinel.datasource.RuleType设置
spring.cloud.sentinel.datasource.rule1.nacos.rule-type=flow

#spring.cloud.sentinel.datasource.rule2.nacos.server-addr=localhost:8848
#spring.cloud.sentinel.datasource.rule1.nacos.namespace=b70655ab-8c47-4dbb-b6b0-589f8eda9441
#spring.cloud.sentinel.datasource.rule2.nacos.data-id=${spring.application.name}
#spring.cloud.sentinel.datasource.rule2.nacos.group-id=DEFAULT_GROUP
#spring.cloud.sentinel.datasource.rule2.nacos.data-type=json
## 熔断规则，参考com.alibaba.cloud.sentinel.datasource.RuleType设置
#spring.cloud.sentinel.datasource.rule2.nacos.rule-type=degrade
```

- 在nacos中相应的位置新增规则配置文件并发布

```json
[
    {
        // 资源名称
        "resource": "getInfo",
        // 来源应用
        "limitApp": "default",
        // 阈值类型：0-线程数，1-QPS
        "grade": 1,
        // 单机阈值
        "count": 1,
        // 流控模式：0-直接，1-关联，2-链路
        "strategy": 0,
        // 流控效果：0-快速失败，1-Warm UP，2-排队等待
        "controlBehavior": 0,
        // 是否集群
        "clusterMode": false
    }
]
```

### 客户端

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

- 应用配置

```properties
# sentinel控制台地址
spring.cloud.sentinel.transport.dashboard=localhost:6060
# 默认8179，如果端口被占用会依次自增直到找到未被占有的端口
spring.cloud.sentinel.transport.port=8719
```

- 控制器类

```java
@RestController
@RequestMapping("/sentinel")
public class SentinelController {
    @SentinelResource(
    // 资源名称，对应sentinel控制台定义规则时的资源名，可以自定义或用请求路径
    value = "getInfo", 
    blockHandlerClass = SentinelBlockHandler.class, 
    // blockHandlerClass指定的类中的方法名称
    blockHandler = "getInfoBlockHandler",
    fallbackClass = SentinelFallback.class,
    // fallbackClass指定的类中的方法名称
    fallback = "getInfoFallback")
    @GetMapping("/getInfo")
    public ResultVo<String> getInfo() {
        return ResultVo.success("hello sentinel");
    }
}
```

- 自定义全局BlockHandler，负责sentinel配置的规则违规场景，自定义信息返回

```java
public class SentinelBlockHandler {
    // 必须为static，@SentinelResource注解的方法的形参怎么定义，此方法也怎么定义，最后再加上异常形参
    public static ResultVo<Object> getInfoBlockHandler(BlockException blockException) {
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), "自定义sentinel返回提示信息"); 
    }
}
```

- 自定义全局Fallback，负责业务异常，自定义异常信息返回

```java
public class SentinelFallback {
    // 必须为static，@SentinelResource注解的方法的形参怎么定义，此方法也怎么定义，最后再加上异常形参
    public static ResultVo<Object> getInfoFallback(Throwable throwable) {
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), "自定义异常返回提示信息"); 
    }
}
```

- fallback 和 blockHandler 都配置，则blockHandler处理

### OpenFeign和Sentinel整合

目的：当接口对外提供服务时，可以将服务提供方的@SentinelResource的fallback放到FeignApi里面的fallback

#### 服务提供方

参考前面写的Sentinel客户端写法

#### FeignApi

- 接口，@FeignClient注解增加fallback属性

```java
@FeignClient(name = "account-service", fallback = AccountApiFallback.class)
public interface AccountApi {
    // 不能在接口上写@RequestMapping了，请求路径要写全（本体handler类上@RequestMapping注解的路径要加上来）
    @GetMapping("/account/getAccount")
    ResultVo<AccountOutputVo> getAccount(@RequestParam("userId") Long userId);
}
```

- fallback，实现接口并覆写方法，调用服务提供方异常时将会走此逻辑

```java
@Component
public class AccountApiFallback implements AccountApi {
    @Override
    public ResultVo<AccountOutputVo> getAccount(Long userId) {
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), "调用account-service失败");
    }
}
```

#### 服务调用方

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

- 配置

```properties
# 如果有通过openFeign调用外部接口，需激活sentinel对feign的支持
feign.sentinel.enabled=true
```

- 主启动类

```java
// 指定openFeign接口所在包
@EnableFeignClients(basePackages = "com.handle.open.feign.api")
// 扫描fallback类所在包
@ComponentScan("com.handle.open.feign.api")
@EnableDiscoveryClient
@SpringBootApplication

public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

## Seata

### seata-server

- 创建seata数据库

- 建表：[postgresql.sql](/sql/seata-server-postgresql.sql)

- 解压seata，进入conf文件夹下，参考application.example.yaml，修改application.yaml

[application.yaml](/yaml/application.yaml)

- 启动seata-server

```sh
bash seata-server.sh
```

- compose.yaml

```yaml
seata:
    container_name: seata01
    image: seataio/seata-server:2.0.0
    ports:
        - "7091:7091"
        - "8091:8091"
    environment:
        # 设置公网ip，seata客户端通过这个ip连接seata服务器
        - SEATA_IP=192.168.56.1
        - SEATA_PORT=8091
    volumes:
        - /handle/data/seata/application.yaml:/seata-server/resources/application.yaml
    networks: 
        - my-docker-net
    restart: always
    depends_on:
        - nacos
```

- 查看nacos服务列表中是否有seata，查看<http://localhost:7091/>是否能够正常访问

### seata-client

- 与seata事务相关的数据库添加表（AT专用）：[postgresql.sql](/sql/seata-client-postgresql.sql)

- 创建测试用账户表[account.sql](/sql/account-postgresql.sql)

- 创建测试用订单表[order.sql](/sql/order-postgresql.sql)

- 创建测试用库存表[inventory.sql](/sql/inventory-postgresql.sql)

- maven dependency

```xml
<!-- seata 版本需要跟seata服务器版本一致 -->
<dependency>
    <groupId>io.seata</groupId>
    <artifactId>seata-spring-boot-starter</artifactId>
    <version>1.3.0</version>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    <exclusions>
        <exclusion>
            <groupId>io.seata</groupId>
            <artifactId>seata-spring-boot-starter</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

- 应用配置

```properties
# seata 配置中心
seata.config.type=nacos
seata.config.nacos.server-addr=localhost:8848
seata.config.nacos.namespace=59ec6563-6292-4f6c-9e68-e2fb3b653823
seata.config.nacos.group=SEATA_GROUP
seata.config.nacos.username=nacos
seata.config.nacos.password=nacos
# seata注册中心
seata.registry.type=nacos
seata.registry.nacos.application=seata-server
seata.registry.nacos.server-addr=localhost:8848
seata.registry.nacos.namespace=59ec6563-6292-4f6c-9e68-e2fb3b653823
seata.registry.nacos.group=SEATA_GROUP
seata.registry.nacos.username=nacos
seata.registry.nacos.password=nacos

# 事务组名称，由它获得TC服务的集群名称
seata.tx-service-group=default_tx_group
# 事务组与TC服务集群的映射关系：事务组名称-TC服务集群名称
# seata.service.vgroup-mapping后面的事务组名称与seata.tx-service-group定义的事务组名称一致
seata.service.vgroup-mapping.default_tx_group=default
seata.data-source-proxy-mode=AT
```

- 在需要全局事务处理的控制器类、业务类实现方法上加@GlobalTransactional注解
