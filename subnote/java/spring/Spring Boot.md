# Spring Boot

- 依赖

```xml
<!-- 适用于聚合项目 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring.boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- 适用于单体项目 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
</parent>
```

## 一个简单的Spring Boot应用

- 依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>4.1.2</version>
</parent>

<groupId>com.handle</groupId>
<artifactId>spring-boot-demo</artifactId>
<version>1.0-SNAPSHOT</version>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- gradle

```kotlin
plugins {
    id("java")
    id("org.springframework.boot") version ("4.2.0")
    id("io.spring.dependency-management") version ("1.1.7")
}

group = "com.handle"
version = "1.0-SNAPSHOT"

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(27)
    }
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
}
```

- 主类

```java
@RestController
@SpringBootApplication
public class Main {
    @GetMapping("test")
    public String test() {
        return String.valueOf(System.currentTimeMillis());
    }

    public static void main(String[] args) {
        // 加载配置，创建IOC容器，启动内置web服务器
        SpringApplication.run(Main.class, args);
    }
}
```

### 启动项目

```sh
mvn spring-boot:run


# 前提是添加了java和org.springframework.boot这两个插件
gradle bootRun
```

## starter

- spring-boot-starter, Core starter, including auto-configuration support, logging and YAML

## 常用注解

### @SpringBootApplication

- @SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan

- @SpringBootConfiguration = @Configuration

- @EnableAutoConfiguration 启用 Spring Boot 的自动配置机制

- @ComponentScan 启用组件扫描，默认扫描当前类所在包及其子包

```java
// 禁用指定的自动配置类
// 如果该自动配置类不在类路径下，用excludeName并指定全限定类名
// 也可以通过配置spring.autoconfigure.exclude来指定，或者这两种方式同时指定
// 此外用@EnableAutoConfiguration也可以实现
@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
```

- 除了 @EnableAutoConfiguration和@SpringBootApplication外，@AutoConfigurationPackage可以定义额外的自动配置包

### `@ConfigurationProperties`

- 作用：配置绑定，将类中所有属性和配置文件中的相关配置进行绑定，默认从application.properties(.yaml)文件中获取值，不用写@Value
  
- 属性：prefix：取前缀为xxx的属性

- 此注解所在的类必须是一个组件，在类上注解

- 可以给集合类型赋值

- 如果是properties文件，并且内容有中文时需要用@PropertySource指定编码格式，并且文件名不能是application.properties

假设配置文件application.yaml存在配置

```yaml
user01:
    name: 张三
```

#### ConfigurationProperties用法1

- @Component + @ConfigurationProperties

```java
@Component
@ConfigurationProperties(prefix = "user01")
@Setter
public class UserDO {
    private String name;
}
```

#### ConfigurationProperties用法2

- @Configuration + @ConfigurationProperties

```java
@Configuration
@ConfigurationProperties(prefix = "user01")
@Setter
public class UserDO {
    private String name;
}
```

#### ConfigurationProperties用法3

- @Configuration + @ConfigurationProperties

```java
@Configuration
public class ApplicationConfiguration {
    // 会自动将配置文件中对应的属性设置到UserDO，然后加入到IOC容器
    @ConfigurationProperties(prefix = "user01")
    @Bean
    public UserDO userDO() {
        return new UserDO();
    }
}
```

#### ConfigurationProperties用法4

- @Configuration + @EnableConfigurationProperties + @ConfigurationProperties

```java
@Setter
@ConfigurationProperties(prefix = "user01")
public class UserDO {
    private String name;
}

@Configuration
@EnableConfigurationProperties(UserDO.class)
public class MainConfiguration {}
```

### `@ImportResource`

- 导入spring的xml配置文件，让其生效
- 可以标注在主启动类上

### `@Mapper`

- 在DAO接口上注解

```java
@Mapper
public interface UserDAO {
 List<User> findAll();
}
```

### `@MapperScan`

如果嫌每个DAO接口都加@Mapper注解太麻烦，可以在主启动类上面加上注解@MapperScan，就会自动扫描指定的包下的DAO

```java
@MapperScan("com.handle.application.dao")
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### `@InitBinder`

为当前控制器注册一个属性编辑器，只对当前Controller有效，参数webDataBinder是用于表单到方法的数据绑定的

### 自定义数据库连接属性并配置

- /config/jdbc.properties

```properties
jdbc.mysql.driver = com.mysql.cj.jdbc.Driver
jdbc.mysql.url = jdbc:mysql://localhost:3306/hr?serverTimezone=GMT%2B8
jdbc.mysql.username = root
jdbc.mysql.password = mysql123
```

- 从jdbc.properties文件获取属性

```java

// @PropertySource：加载指定配置文件,第二個路徑為另一個jdbc.properties在磁盤上（jar包外）的路徑

// @ConstructorBinding 构造函数注入只能提取application.properties(.yaml)文件中的值
// 想要提取别的配置文件中的值，需要通过@Component，同时必须设置getter和setter
@Getter
@ConstructorBinding
@PropertySource(value = {"classpath:config/jdbc.properties", "file:${spring.profiles.path}/jdbc.properties"}, ignoreResourceNotFound = true)
@ConfigurationProperties(prefix = "jdbc.mysql")
public class JdbcProperties {
    private String driver;

    private String url;

    private String username;

    private String password;

    public JdbcProperties(String driver, String url, String username, String password) {
        this.driver = driver;
        this.url = url;
        this.username = username;
        this.password = password;
    }
}
```

- 从properties类的对象获取属性来配置连接池

```java
@Configuration
@EnableConfigurationProperties(JdbcProperties.class)
public class JdbcConfig {
    // @Bean 将方法的返回值添加到容器之中,并且容器中这个组件的id就是方法名
    // 此处作为参数的JdbcProperties jdbcProperties 直接从容器中捞到值
    @Bean
    public DruidDataSource dataSource(JdbcProperties properties) {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(properties.getDriver());
        druidDataSource.setUrl(properties.getUrl());
        druidDataSource.setUsername(properties.getUsername());
        druidDataSource.setPassword(properties.getPassword());
        return druidDataSource;
    }

}
```

## 测试

- maven依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

- 测试类

```java
@SpringBootTest
public class ApplicationTest {
    @Autowired
    private User user;
 
    @Test
    public void test() {
    System.out.println(user);
    }
}

```

## 获取应用所在目录

- 用ApplicationHome

```java
// 底层代码还是调用了getProtectionDomain，但做了修正，更可靠稳定
ApplicationHome home = new ApplicationHome(Application.class);

// java -jar file.jar为：/path/to/file.jar
// ide为：/path/to/target/classes
File jarFile = home.getSource();

// java -jar file.jar为：/path/to
// ide为：/path/to/target/classes，已经确认，不是输出错了
File dir = home.getDir();
```

- 用getProtectionDomain

```java
// java -jar file.jar为：/path/to/app.jar
// ide为：/path/to/project/target/classes
String jarPath = new File(
    Application.class.getProtectionDomain()
        .getCodeSource()
        .getLocation()
        .toURI()
).getAbsolutePath();
```

## 整合AOP

- 导入如下依赖就可以用@Aspect注解声明切面了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

## 整合声明式事务

- 导入如下依赖就可以用@Transactional注解声明事务了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

## 整合Spring MVC

- 导入如下依赖就可以了，Spring MVC组件、全局异常和拦截器和以前一样使用

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## 整合Mybatis

- 依赖

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
<!-- 其包含了spring-boot-starter-jdbc -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
</dependency>
```

- 启动类

```java
// mapper.java所在包
@MapperScan("com.handle.application.dao")
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 配置文件

```conf
# 数据库设置，千万不要设置到hikari目录下
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/handle
spring.datasource.username=postgres
spring.datasource.password=postgres123

# 指定mapper.xml文件位置
mybatis.mapper-locations=classpath:mapper/**/*.xml
# 设置实体别名的包位置
mybatis.type-aliases-package=com.handle.application.pojo
# 设置表字段名-实体属性自动映射：account_id accountId 自动映射
mybatis.configuration.map-underscore-to-camel-case=true
# 设置对于resultMap标签，无论有没有嵌套（association/collection）都自动映射result标签的列名和属性
mybatis.configuration.auto-mapping-behavior=full
# 设置日志输出实现
mybatis.configuration.log-impl=org.apache.ibatis.logging.slf4j.Slf4jImpl
```

## SpringApplication

### 懒加载

当使用到某个组件时才去加载它

优点：启动速度快

缺点：延迟问题发现，在使用懒加载前，需要调整好JVM的堆大小

```yaml
spring:
    main:
        # 启用懒加载，默认禁用
        lazy-initialization: true
```

- 对指定组件启用/禁用懒加载

```java
@Bean
@Lazy(false)
public MyBeanName myBeanName() {
    return new MyBeanName();
}
```

### 自定义SpringApplication

```java
/**
 * 启动项目后会生成存放 pid 的 application.pid 文档
 * pid存放到指定目录：在application.properties(yaml)配置文档中添加spring.pid.file=/var/log/app.pid
 */
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(Application.class);


    // 生成进程pid文档
    app.addListeners(new ApplicationPidFileWriter());

    // 关闭banner
    app.setBannerMode(Banner.Mode.OFF);


    app.run(args);
}
```

### 应用可用性状态

#### 准备就绪状态

期望在启动期间运行的任务应该由 CommandLineRunner and ApplicationRunner 组件完成

而不是使用 Spring 组件生命周期回调，比如 @PostConstruct

#### 管理应用可用性状态

应用组件在任何时候都可以注入 ApplicationAvailability 然后调用其方法来获得当前的可用性状态

而通常是监听应用状态更新或者更新应用状态

##### 监听应用状态更新

```java
@Component
public class MyReadinessStateExporter {
    @EventListener
    public void onStateChange(AvailabilityChangeEvent<ReadinessState> event) {
        switch (event.getState()) {
            case ACCEPTING_TRAFFIC -> {
                // create file /tmp/healthy
            }
            case REFUSING_TRAFFIC -> {
                // remove file /tmp/healthy
            }
        }
    }
}
```

##### 更新应用状态

```java
@Component
public class MyLocalCacheVerifier {

    private final ApplicationEventPublisher eventPublisher;

    public MyLocalCacheVerifier(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    public void checkLocalCache() {
        try {
            // ...
        }
        catch (CacheCompletelyBrokenException ex) {
            AvailabilityChangeEvent.publish(this.eventPublisher, ex, LivenessState.BROKEN);
        }
    }
}
```

### 应用事件和监听器

除了 Spring 框架的事件外（如 ContextRefreshedEvent）

SpringApplication 发送一些附加的应用事件

一些事件在 ApplicationContext 创建之前被触发，无法使用 @Bean 注册监听它们

但可以用 SpringApplication.addListeners(…​) 或 SpringApplicationBuilder.listeners(…​) 方法来注册监听它们

- 当应用启动时，应用事件发送顺序如下
    - ApplicationStartingEvent 在应用启动时发送，但在任何处理之前发送（除了监听器和初始化器的注册）
    - ApplicationEnvironmentPreparedEvent 当要在上下文中使用的环境已知时，但在创建上下文之前发送
    - ApplicationContextInitializedEvent 当  ApplicationContext 准备好且 ApplicationContextInitializers 被调用，但在加载任何 bean definitions 前发送
    - ApplicationPreparedEvent 在刷新开始之前、加载 bean definitions 之后发送
    - ApplicationStartedEvent 在上下文刷新之后、调用任何 application 和 command-line runners 之前发送
    - AvailabilityChangeEvent 紧接着上一步以 LivenessState.CORRECT 状态发送，表示应用程序上线了（live）
    - ApplicationReadyEvent 调用任何 application 和 command-line runners 后发送
    - AvailabilityChangeEvent 紧接着上一步以  ReadinessState.ACCEPTING_TRAFFIC 状态发送，表示应用程序准备好接收服务请求了
    - ApplicationFailedEvent 如果在启动时出现异常，则发送

上面的列表仅包含与 SpringApplication 绑定的 SpringApplicationEvent

- 此外，下面的事件在 ApplicationPreparedEvent 之后， ApplicationStartedEvent 前
    - WebServerInitializedEvent WebServer准备好后发送
        - ServletWebServerInitializedEvent 和 ReactiveWebServerInitializedEvent 分别对应 servlet 和 reactive 变体
    - ContextRefreshedEvent 当刷新 ApplicationContext 时发送

应用程序事件通过使用 Spring 框架的事件发布机制发送

该机制的部分功能是确保将发布到子上下文中的监听器的事件也发布到任何祖先上下文中的监听器

因此，如果应用程序使用 SpringApplication 实例的层次结构

则监听器可能会接收到相同类型的应用程序事件的多个实例

为了使监听器能够区分其上下文的事件和后代上下文的事件

它应该请求注入其应用程序上下文，然后将注入的上下文与事件的上下文进行比较

可以通过实现ApplicationContextAware注入上下文，如果监听器是bean，可以使用@Autowired

### 网络环境

SpringApplication 尝试创建正确的 ApplicationContext 类型，推断 WebApplicationType 机制如下

如果 Spring MVC 存在，则使用 AnnotationConfigServletWebServerApplicationContext

如果 Spring MVC 不存在且 Spring WebFlux 存在，则使用 AnnotationConfigReactiveWebServerApplicationContext

否则使用 AnnotationConfigApplicationContext

```java
SpringApplication application = new SpringApplication(Main.class);
// 还可以手动设置类型
// 如在 JUnit 测试可以这样设置
application.setWebApplicationType(WebApplicationType.NONE);
```

### 访问应用参数

只要注入 ApplicationArguments 组件，就可以访问传给 SpringApplication.run(…​) 方法的参数

ApplicationArguments 接口提供对原始 String[] 参数以及解析后的选项和非选项参数的访问

```java
@Component
public class MyBean {
    public MyBean(ApplicationArguments args) {
        // 选项参数
        boolean debug = args.containsOption("debug");
        // 非选项参数
        List<String> files = args.getNonOptionArgs();
        if (debug) {
            System.out.println(files);
        }
        // if run with "--debug logfile.txt" prints ["logfile.txt"]
    }
}
```

Spring Boot 也注册了一个 CommandLinePropertySource 组件

你甚至可以用 @Value 来获取单个参数值

```java
@RestController
public class MainController {
    @Value("${debug:false}")
    private String esDebugEnabled;

    @GetMapping("/")
    public String test() {
        return String.valueOf(System.currentTimeMillis() + esDebugEnabled);
    }
}
```

启动参数加上`--debug=true`就可以获取到

### 使用 ApplicationRunner 或 CommandLineRunner

如果想要在 SpringApplication 启动的时候执行一些特定的代码

可以实现 ApplicationRunner 或 CommandLineRunner 接口

两者都以相同的方式工作，提供了一个 run 方法，这个方法会在 SpringApplication.run(…​) 完成之前被调用

它们很适合执行 SpringApplication 启动后开始处理请求前的任务

CommandLineRunner 提供对应用参数以字符串数组形象访问的支持

ApplicationRunner 则使用前面提到的 ApplicationArguments

```java
@Component
public class MyCommandLineRunner implements CommandLineRunner {

    @Override
    public void run(String... args) {
        // Do something...
    }

}
```

如果定义了好几个  CommandLineRunner 或 ApplicationRunner 组件并且必须以特定顺序调用

则可以实现Ordered接口或使用@Order注解来实现

### 应用退出

每个 SpringApplication 都注册了一个 shutdown 钩子来确保在JVM退出时 ApplicationContext 正确关闭

所有标准 Spring 生命周期回调如 DisposableBean 接口 或 @PreDestroy 都可以使用

此外，如果想要在 SpringApplication.exit() 被调用时返回特定的退出码

可以实现  ExitCodeGenerator 接口

此退出码可以传给 System.exit() 如下面的例子

```java
@SpringBootApplication
public class MyApplication {

    @Bean
    public ExitCodeGenerator exitCodeGenerator() {
        return () -> 42;
    }

    public static void main(String[] args) {
        // 启动后就关闭了...
        // 可以看到 Process finished with exit code 42
        System.exit(SpringApplication.exit(SpringApplication.run(MyApplication.class, args)));
    }
}
```

异常类也可以实现 ExitCodeGenerator 接口

通过实现 getExitCode() 方法

当该异常发生时，Spring Boot 返回  getExitCode() 方法提供的退出码

如果有多个 ExitCodeGenerator ，生成的第一个非0退出码会被使用

为了控制最终调用哪个，可以实现Ordered接口或使用@Order注解

## 日志

```yaml
spring:
    main:
        # turned off startup information logging
        # 此外还可以继承SpringApplication并重写logStartupInfo(boolean) 来添加额外的日志
        log-startup-info: false
    mvc:
        # 记录请求明细日志
        log-request-details: true
logging:
    level:
        # 默认兜底的日志基本，优先级比下面列出的具体包要低
        root: INFO
        # 其它想要自定义日志级别的具体包
        com.example.service: DEBUG
```

```sh
# --debug 启用调试，打印调试日志
java -jar app.jar --debug
```

## 打包

- spring boot原文：
    - The spring-boot-starter-parent POM includes `<executions>` configuration to bind the repackage goal.
    - If you do not use the parent POM, you need to declare this configuration yourself.

- 依赖

```xml
<build>
    <!-- 声明插件版本，供子模块使用（不用写version），从而实现版本锁定 -->
    <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring.boot.version}</version>
                    <!-- 子项目只需添加groupId、artifactId，executions对所有子项目生效 -->
                    <executions>
                        <execution>
                            <id>repackage</id>
                            <goals>
                                <goal>repackage</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
    </pluginManagement>
</build>
```

### 发布

- 1.复制jre、可执行jar文档、run.bat（springboot项目看需求创建config文档夹，里面放application.properties配置文档），到同一目录下即可
- 2.运行run.bat快速启动可执行jar文档

## 命令启动

```sh
java -jar [选项] [参数] <jar文件名>
```

- 指定系统属性-D<属性名>=<属性值>，可以通过System.getProperty()在应用程序获取该属性值
    - 指定服务器端口：`-Dserver.port=80`
    - 指定要激活的配置文件：`-Dspring.profiles.active=<dev/test/prod>`

- 指定JVM参数`-X`
    - 设置最小堆内存：`-Xms1024m`
    - 设置最大堆内存：`-Xmx1024m`

## Spring Boot项目安装ssl证书，用https访问

```yaml
server:
    port: 8443
    ssl:
        enabled: true
        # 记得将keystore文件复制到resources目录下
        key-store: classpath:yourKeystore.p12
        key-store-password: changeit
        key-store-type: PKCS12
        # keystore文件只包含一个证书的时候可以不用写别名
        key-alias: yourKeystoreAlias
```

## Developer Tools

一个用来快速启动的开发工具

使用两个类加载器来快速重启项目
    - 基础类加载器，加载依赖jar
    - 重启类加载器，加载开发的源码

开发工具会监听类路径的所有目录的文件变更，只要类路径下的文件更新就会触发重启

当重启出问题后，如果是此开发工具的问题，手动重新启动项目就可以了

- 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```
