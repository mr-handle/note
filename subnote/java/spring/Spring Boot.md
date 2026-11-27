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

#### ConfigurationProperties用法5

```java
@Configuration(proxyBeanMethods = false)
public class ThirdPartyConfiguration {
    @Bean
    @ConfigurationProperties("another")
    public AnotherComponent anotherComponent() {
        return new AnotherComponent();
    }
}
```

#### 使用@ConfigurationProperties类

可以像注入spring组件那样注入@ConfigurationProperties类，从而使用它

```java
@Service
public class MyService {

    private final MyProperties properties;

    public MyService(MyProperties properties) {
        this.properties = properties;
    }

    public void openConnection() {
        Server server = new Server(this.properties.getRemoteAddress());
        server.start();
        // ...
    }

    // ...
}
```

#### 保留自定义的字段名

当属性名和字段名不一样时，可以通过@Name来设置

```java
@Component
@ConfigurationProperties(prefix = "user01")
@Setter
public class UserDO {
    @Name("name")
    private String username;
}
```

#### 构造方法绑定

```java
@ConfigurationProperties(prefix = "user01")
public class UserDO {
    private String username;

    // 形参名称跟属性名设置成一样
    public UserDO(String name) {
        this.username = name;
    }
}

@ConfigurationProperties(prefix = "user01")
public class UserDO {
    private String username;

    // 保留自定义的形参名称，用@Name指定属性名
    public UserDO(@Name("name") String username) {
        this.username = username;
    }
}

@ConfigurationProperties(prefix = "user01")
public class UserDO {
    private String username;

    public UserDO() {
    }

    // 如果有多个构造方法，在使用构造器绑定的方法上添加此注解
    @ConstructorBinding
    public UserDO(String name) {
        this.username = name;
    }
}

@Configuration
// 构造方法绑定必须通过添加此注解来完成
@EnableConfigurationProperties(UserDO.class)
public class MainConfiguration {}
```

### `@DefaultValue`

```yaml
user01:
    name: handle
```

```java
@ConfigurationProperties(prefix = "user01")
@ToString
public class UserDO {
    private String username;

    private Pet pet;

    // 场景1
    // 由于没有写pet属性，直接没有实例化pet
    // UserDO(username=handle, pet=null)
    public UserDO(String name, Pet pet) {
        this.username = name;
        this.pet = pet;
    }

    @ToString
    public static class Pet {
        private String name;

        public Pet(String name) {
            this.name = name;
        }
    }
}

@ConfigurationProperties(prefix = "user01")
@ToString
public class UserDO {
    private String username;

    private Pet pet;

    public UserDO(String name, @DefaultValue Pet pet) {
        this.username = name;
        this.pet = pet;
    }

    @ToString
    public static class Pet {
        private String name;

        // 场景二
        // 由于没有写pet属性，直接没有实例化pet，因此此处@DefaultValue也不会生效
        public Pet(@DefaultValue("cat") String name) {
            this.name = name;
        }
    }
}

@ConfigurationProperties(prefix = "user01")
@ToString
public class UserDO {
    private String username;

    private Pet pet;

    // 场景三
    // 虽然没有写pet属性，但添加了@DefaultValue注解，最终会实例化一个pet，字段为默认值
    public UserDO(String name, @DefaultValue Pet pet) {
        this.username = name;
        this.pet = pet;
    }

    @ToString
    public static class Pet {
        private String name;

        public Pet(String name) {
            this.name = name;
        }
    }
}
```

```yaml
user01:
    name: handle
    # 场景4
    # 由于制订了pet属性，最终会实例化一个pet，字段为默认值
    # 最终效果和场景3一样
    pet: {}
```

```java
@ConfigurationProperties(prefix = "user01")
@ToString
public class UserDO {
    private String username;

    private Pet pet;

    public UserDO(String name, Pet pet) {
        this.username = name;
        this.pet = pet;
    }

    @ToString
    public static class Pet {
        private String name;

        public Pet(String name) {
            this.name = name;
        }
    }
}
```

### `@EnableConfigurationProperties`

在开发自定义自动装配或想让它们根据条件装配的时候

@ConfigurationProperties注解的类不适合配置扫描

这时候可以使用@EnableConfigurationProperties指定要绑定和注册为bean的@ConfigurationProperties类

@EnableConfigurationProperties可以在任何@Configuration类上注解

```java
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(SomeProperties.class)
public class MyConfiguration {}

@ConfigurationProperties("some.properties")
public class SomeProperties {}
```

### `@ConfigurationPropertiesScan`

通常加在@SpringBootApplication所在的主类上

也可以加在@Configuration类上

```java
@SpringBootApplication
// 如果不指定扫描哪些包，默认从当前类所在包开始扫描
@ConfigurationPropertiesScan({ "com.example.app", "com.example.another" })
public class MyApplication {}
```

官方建议@ConfigurationProperties类只处理环境值，不要注入其它bean

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

## 外部配置

### 访问命令行属性

默认情况下，SpringApplication转换任何命令行选项参数（以--开头的命令行参数，如--server.port=9000）

并将他们添加到 Spring Environment

如果不想让命令行属性添加到 Spring Environment

```java
SpringApplication application = new SpringApplication(Main.class);
application.setAddCommandLineProperties(false);
application.run(args);
```

### 特定Profile文件

除了application.properties(yaml)配置文件

Spring Boot 也尝试加载命名为application-{profile}格式的特定Profile文件

如果激活了application-{profile}文件，则application和application-{profile}都会生效

它们在相同的目录下被加载，并且application-{profile}会覆盖该目录下非profile文件的配置

如果多个application-{profile}文件被激活

```yaml
spring:
    profiles:
        # live会覆盖prod的配置
        active: prod,live
```

Environment有一组默认配置文件，如果没有激活任何profile，默认会使用application-default的配置

### 属性占位符

```yaml
# 属性占位符属性名写法：应该坚持用减号连接单词小写的写法
# ${property-name}能关联：property-name、propertyName和PROPERTYNAME
# 而${propertyName}只能关联：propertyName和PROPERTYNAME
# 这样写可以引用JVM系统属性和操作系统环境变量的值
${property-name}

# 指定默认值
${property-name:defaultValut}
```

### 多文档文件

将一个文档分成多个profile

这个文档不能用 @PropertySource 或 @TestPropertySource加载

一般用于条件激活profile的场景

- yaml用三个减号表示上一文档内容结束下一文档开始

```yaml
spring:
    application:
        name: "MyApp"
---
spring:
    application:
        name: "MyCloudApp"
    config:
        activate:
            on-cloud-platform: "kubernetes"
```

- properties用 #--- 或 !--- 表示上一文档内容结束下一文档开始

```properties
spring.application.name=MyApp
#---
spring.application.name=MyCloudApp
spring.config.activate.on-cloud-platform=kubernetes
```

### 激活属性

```yaml
myprop:
"always-set"
---
spring:
    config:
        # 当运行在kubernetes中，且prod或staging profile激活了，才激活此profile
        activate:
            # 当运行在kubernetes中才生效
            on-cloud-platform: "kubernetes"
            # prod或staging profile激活了才生效
            on-profile: "prod | staging"
myotherprop: "sometimes-set"
```

### 加密属性

Spring Boot没有内置加密属性值的功能

可以使用EnvironmentPostProcessor接口，在应用启动之前对Environment进行操作

此外官方推荐用Spring Cloud Vault

### YAML

YAML是JSON的超集

只要类路径上有SnakeYAML库，SpringApplication类就会自动支持YAML作为属性的替代

只要使用了spring-boot-starter，就有了SnakeYAML库

YAML文件不能用 @PropertySource 或 @TestPropertySource加载

如果想要用@PropertySource 或 @TestPropertySource加载，可以用properties文件

- Spring框架提供了两个类来加载YAML文件
    - YamlPropertiesFactoryBean 将YAML文件加载为Properties
    - YamlMapFactoryBean 将YAML文件加载为Map
    - YamlPropertySourceLoader 需要手动将YAML文件加载为PropertySource

### 配置随机属性值

```yaml
my:
    # 字符串
    secret: "${random.value}"
    number: "${random.int}"
    bignumber: "${random.long}"
    uuid: "${random.uuid}"
    number-less-than-ten: "${random.int(10)}"
    number-in-range: "${random.int[1024,65536]}"
```

### 配置系统环境属性

```java
SpringApplication application = new SpringApplication(Main.class);
// 设置系统环境属性名前缀
// 这样属性remote.timeout的值就从系统环境INPUT_REMOTE_TIMEOUT中得到
application.setEnvironmentPrefix("input");
application.run(args);
```

### 绑定Map

```yaml
my:
    map:
        # 有中括号没双引号，中括号会保留，键：[/key0]
        [/key0]: "value0"
        # 有中括号有双引号，正确解析
        "[/key1]": "value1"
        "[/key2]": "value2"
        # 无中括号有双引号，特殊字符会被删除，键：key3
        "/key3": "value3"
```

当键包含非字母、数字、`-`或 `.`时，想要保留原来的键的值，需要用中括号包裹，然后再用双引号包裹

上面的例子中的属性将会绑定为：[/key0], /key1, /key2 和 key3

### 合并复杂类型

对于复杂类型的属性，如list、map，合并规则是覆盖

```java
@ConfigurationProperties("my")
@Getter
public class MyProperties {
    private final List<MyPojo> list = new ArrayList<>();
}
```

```yaml
my:
    list:
    -   name: "my name"
        description: "my description"
---
spring:
    config:
        activate:
            on-profile: "dev"
my:
    list:
    -   name: "my another name"
```

如果dev profile没有启用，则 MyProperties.list包含一个MyPojo（name: "my name"，description: "my description"）

如果dev profile启用了，则则 MyProperties.list包含一个MyPojo（name: "my another name"，description: null）

## Profile

```yaml
spring:
    profiles:
        # include profile在任何spring.profiles.active前被加载
        include:
            - "common"
            - "local"
```

## 日志

### 日志格式

- 日期时间
- 日志级别：ERROR, WARN, INFO, DEBUG, TRACE
    - logback没有FATAL级别，它映射到ERROR
- 进程号
- `---` 分隔符，区分实际日志消息的开始
- [应用名] 前提是设置了spring.application.name
- [应用组] 前提是设置了spring.application.group
- 线程名
- 关联ID 如果启用了跟踪
- 日志记录器名称 通常是全类名（缩写形式）
- 具体日志消息

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
    # 不记录应用名，前提是设置了spring.application.name
    include-application-name: false
    # 不记录应用组，前提是设置了spring.application.group
    include-application-group: false
    console:
        # 禁用控制台日志打印
        enabled: false
    level:
        # 默认兜底的日志基本，优先级比下面列出的具体包要低
        root: INFO
        # 其它想要自定义日志级别的具体包
        com.example.service: DEBUG
```

### 控制台输出

#### 启用调试模式的两种方法

- 方法二，在配置文件中设置

```yaml
# 启用调试模式
debug: true

# 打印更多消息
trace: true
```

- 方法二，在启动应用的命令行设置

```sh
# --debug 启用调试模式，打印调试日志
java -jar app.jar --debug

# 打印更多消息
java -jar app.jar --trace
```

#### 配置日志输出颜色

如果终端支持ANSI，配置日志输出颜色的可读性更好

```yaml
spring:
    output:
        ansi:
            # detect,always,never
            enabled: detect
```

### 文件输出

- logging.file.name 指定日志文件，可以是绝对或相对路径

- logging.file.path 指定日志文件目录，可以是绝对或相对路径

当两者都指定时，logging.file.name生效

日志文件在达到10MB时进行滚动

### 文件滚动

如果使用logback或log4j2，可以通过应用配置设置文件滚动

- logback设置

```yaml
logging:
    logback:
        rollingpolicy:
            # 文件名格式
            file-name-pattern: ${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz
            # 是否在启动时清理日志文件
            clean-history-on-start: false
            # 归档前文件的最大容量
            max-file-size: 10MB
            # 归档文件在被删除之前所能容纳的最大总容量
            total-size-cap: 512MB
            # 保留的归档文件最大数量
            max-history: 7
```

### 日志级别

日志级别有：TRACE, DEBUG, INFO, WARN, ERROR, FATAL, 或 OFF

可以通过：`logging.level.<logger-name>=<level>`来设置日志级别

注意这里的logger-name是包名

```yaml
logging:
    level:
        root: "warn"
        org.springframework.web: "debug"
```

### 日志组

- Spring Boot 有两个预定义的日志组
    - web 记录器有：
        - org.springframework.core.codec
        - org.springframework.http
        - org.springframework.web
        - org.springframework.boot.actuate.endpoint.web
        - org.springframework.boot.web.servlet.ServletContextInitializerBeans
    - sql 记录器有：
        - org.springframework.jdbc.core
        - org.hibernate.SQL
        - LoggerListener

```yaml
logging:
    group:
        # 定义日志组
        tomcat: "org.apache.catalina,org.apache.coyote,org.apache.tomcat"
    level:
        # 设置日志组的日志级别
        tomcat: "trace"
```

### 定义日志配置

日志初始化是在创建ApplicationContext前发生的，因此不能通过@Configuration配置文件的@PropertySources来定义日志配置

改变日志系统或禁用它只能通过系统属性

对于logback，配置文件logback-spring.xml和logback.xml都可以，spring boot推荐前者

因为后缀不能让spring完全控制log初始化

Spring Boot 存在一种‌“自动映射机制”‌

它将部分常用的 Spring Environment 属性（来自 application.yml/properties 或环境变量）

自动转换为 Java ‌System Properties‌

由于 Logback 原生支持读取 System Properties

因此对于这些特定的属性，‌不需要‌在 logback-spring.xml 中手动编写 `<springProperty>` 标签即可直接使用

如可以根据官方支持自动映射配置，配置了Spring Environment 就可以在logback配置文件里面使用该系统属性了

```yaml
logging:
    structured:
        format:
            # 可以是ecs、gelf、logstash
            # 控制台日志格式
            console: ecs
            # 文件日志格式
            file: ecs
```

```xml
<!-- replace your encoder with StructuredLogEncoder -->
<encoder class="org.springframework.boot.logging.logback.StructuredLogEncoder">
    <format>${CONSOLE_LOG_STRUCTURED_FORMAT}</format>
    <charset>${CONSOLE_LOG_CHARSET}</charset>
</encoder>
```

### 结构化日志

使用ecs日志格式时，可以自定义如下属性

```yaml
logging:
    structured:
        ecs:
            service:
                # 如果不指定，默认是spring.application.name
                name: MyService
                # 如果不指定，默认是spring.application.version
                version: 1.0
                environment: Production
                node-name: Primary
```

#### 自定义JSON结构化日志

```yaml
logging:
    structured:
        json:
            # 排除日志级别信息
            exclude: log.level
            # 重命名process.id为procid
            rename:
                process.id: procid
            add:
                # 添加固定字段corpname
                corpname: mycorp
```

#### 自定义结构化日志追踪

```yaml
logging:
    structured:
        json:
            stacktrace:
                root: first
                max-length: 1024
                include-common-frames: true
                include-hashes: true
```

#### 自定义结构化日志打印内容

实现StructuredLogFormatter 接口，logback要用ILoggingEvent泛型

```java
class MyCustomFormat implements StructuredLogFormatter<ILoggingEvent> {
    @Override
    public String format(ILoggingEvent event) {
        return "time=" + event.getInstant() + " level=" + event.getLevel() + " message=" + event.getMessage() + "\n";
    }
}
```

然后设置logging.structured.format.console或logging.structured.format.file的值为该实现类的全类名

### Logback扩展

前提是使用logback-spring.xml或定义logging.config属性

#### 特定profile配置

```xml
<configuration>
    <springProfile name="staging">
        <!-- configuration to be enabled when the "staging" profile is active -->
    </springProfile>

    <springProfile name="dev | staging">
        <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
    </springProfile>

    <springProfile name="!production">
        <!-- configuration to be enabled when the "production" profile is not active -->
    </springProfile>
</configuration>
```

#### 环境属性

```xml
<!-- source 命名规则是用减号连接符连接起来的，例如my.property-name  -->
<springProperty scope="context" name="fluentHost" source="myapp.fluentd.host"
        defaultValue="localhost"/>
<appender name="FLUENT" class="ch.qos.logback.more.appenders.DataFluentAppender">
    <remoteHost>${fluentHost}</remoteHost>
    ...
</appender>
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
