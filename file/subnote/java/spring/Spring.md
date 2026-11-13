# Spring

- 启动时Spring扫描所有XML配置或注解
- 把每个Bean的信息解析成BeanDefinition对象，存到Map中
- BeanFactory利用反射机制实例化Bean对象，并递归填充其属性

## IOC

读取配置文件声明的组件（Bean）信息，转成BeanDefinition对象，然后根据BeanDefinition对象反射创建Bean对象，并管理Bean对象的生命周期

作用：削减计算机程序的耦合（降低代码之间的依赖关系）

### IOC容器

- `BeanFactory`：它在构建核心容器时，创建对象采用`延迟加载`的方式，什么时候根据id获取对象，什么时候才真正创建对象。`多例对象适用`。
- `ApplicationContext`：它在构建核心容器时，创建对象采用`立即加载`的方式，只要一读取完配置文件就马上创建配置文件中配置的对象。`单例对象适用`。

#### ApplicationContext的三个常用实现类

- `AnnotationConfigApplicationContext`，读取注解创建容器
- `ClassPathXmlApplicationContext`，加载类路径下的配置文件
- `FileSystemXmlApplicationContext`，加载磁盘任意路径下的配置文件（必须有访问权限）

### IOC注解

#### 自定义类使用的注解

- @Component，当不方便区分是哪一层时使用
- @Controller
- @Service
- @Repository

注：当注解的value属性不写时，默认为所注解的类的类名首字母小写

#### 第三方类使用的注解

#### @Bean

- 在配置类的方法上添加此注解，把当前方法的返回值存入spring的ioc容器中

- 当注解的方法有参数时，spring框架会去容器中查找是否有可用的bean对象，查找的方式和@AutoWired一样

```java
@Configuration
public class ApplicationContextConfiguration {
    // name属性用于指定bean的id，不指定时默认是当前方法名
    @Bean(name = "dataSource")
    public DruidDataSource druidDataSource() {
        return new DruidDataSource();
    }
}
```

#### 引用其它Bean

```java
@Configuration
public class ApplicationContextConfiguration {
    @Bean(name = "dataSource")
    public DruidDataSource druidDataSource() {
        return new DruidDataSource();
    }    
    
    @Bean
    public JdbcTemplate jdbcTemplate() {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        // 使用场景1，由于dataSource是用@Bean创建的，所以这里可以直接调用
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
    
    // 使用场景2，通过声明形参调用，IOC容器必须要有对应类型的组件，否则会报错
    // 当有多个实例的时候，形参名要和对应的Bean的id一致；或者指定@Qualifier属性和对应的Bean的id一致
    // 2.1形参名要和对应的Bean的id一致
    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
    
    // 2.2@Qualifier属性和对应的Bean的id一致
    @Bean
    public JdbcTemplate jdbcTemplate(@Qualifier("dataSource") DataSource dataSource) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
}
```

### 配置类注解

#### @Configuration

- 表示所注解的类是一个配置类，可替代Spring的bean.xml配置文件
- 类似配置包扫描、读取配置文件、定义Bean等功能，bean.xml中可以做，配置类中一样也可以做

```java
@Configuration
// 1.配置包扫描
@ComponentScan(basePackages = {"com.handle.application"})
// 2.读取配置文件
@PropertySource(value = {"classpath:jdbc.properties"})
public class ApplicationConfiguration {
    /**
     * 3.定义Bean
     */
    @Bean
    public DruidDataSource dataSource() {
        return new DruidDataSource();
    }
}
```

#### @Import

- 用于导入其他的配置类

- 注解所在类是父配置类

- 其value属性指定的类都是子配置类，子配置类不用写@Configuration

```java
@Configuration
@Import(SubConfiguration.class)
public class MainConfiguration {}

public class SubConfiguration {}
```

#### 加载配置类的方式

- 1.配置类作为创建AnnotationConfigApplicationContext对象的参数加载，如果有多个配置类其关系为并列关系，这些配置类可不写@Configuration

```java
public class SubConfiguration1 {}

public class SubConfiguration2 {}

public class ApplicationTest {
    @Test
    public void test() {
        ApplicationContext context = new AnnotationConfigApplicationContext(SubConfiguration1.class, SubConfiguration2.class);
    }
}
```

- 2.其它的配置类作为子配置类，在主配置类用@Import注解导入，然后把主配置类作为创建AnnotationConfigApplicationContext对象的参数加载

```java
@Import(value = {SubConfiguration1.class, SubConfiguration2.class})
public class MainConfiguration {}

public class SubConfiguration1 {}

public class SubConfiguration2 {}

public class ApplicationTest {
    @Test
    public void test() {
        ApplicationContext context = new AnnotationConfigApplicationContext(MainConfiguration.class);
    }
}
```

### 配置包扫描的注解

- @ComponentScan

- 指定spring创建容器时要扫描的包，相当于在bean.xml配置了：

```xml
<context:component-scan base-package="package1,package2,..." />
```

- 该注解指定的包中含有配置类，也会扫描这些配置类，这时不用写@Import(会被扫描的配置类名.class)

```java
// DataSourceConfiguration会被扫描到，不用写@Import(DataSourceConfiguration.class)
@Configuration
@ComponentScan(basePackages = {"com.handle.application"})
public class ApplicationConfiguration {}


package com.handle.application.configuration;

@Configuration
public class DataSourceConfiguration {}
```

### 配置文件注解

#### @PropertySource

- encoding，指定配置文件编码格式，如果是`.properties`文件并且文件内容有中文需要指定编码格式

- 用于加载指定的配置文件

- person.yaml

```yaml
user01:
    name: 张三
```

- 将配置文件中person.name的值封装到Person对象

```java
@Component
@PropertySource(value = "classpath:person.yaml")
@ConfigurationProperties(prefix = "user01")
public class Person {
    private String name;
}
```

#### @ImportResource
  
- 作用：导入Spring配置文件(springConfig.xml)
  
- 用法：@Configuration + @ImportResource("classpath:xxx.xml") 一起用

例子：读取spring.xml文件中的Pet

1. 定义宠物类

```java
@Getter
@Setter
@ToString
public class Pet {
    private String name;

    private int age;
}
```

2.设置spring.xml配置Pet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="pet" class="com.handle.entities.Pet">
        <property name="name" value="cat"></property>
        <property name="age" value="2"></property>
    </bean>
</beans>
```

3.定义配置类

```java
@Configuration
@ImportResource("classpath:spring.xml")
public class MainConfiguration {}
```

### DI注解

Spring的三大依赖注入方式：构造器注入、字段注入、Setter注入

构造器注入适合处理必需的依赖项

而Setter注入则更适合可选的依赖项，这些依赖项可以有默认值或在对象生命周期中动态设置

```java
// 构造器注入，依赖字段可以定义为final（官方推荐）
@Controller
public class MyController {
    private final MyService myService;

    // 在只有一个构造器的情况下，Spring会自动把它当成需要注入的构造器,写不写@Autowired都可以
    public MyController(MyService myService) {
        this.orderService = myService;
    }
}

// 字段注入
@Component
public class MyController {
    @Autowired
    private MyService myService;
}

// Setter注入
@Controller
public class XxxController {
    private MyService myService;

    @AutoWired
    public void setMyService(MyService myService) {
        this.myService = myService;
    }
}
```

- 工具类的静态方法使用Spring Bean

```java
// 方法1
// 使用SpringBeanHolder，它对所有工具类都适用，然后在工具类里面获取Spring Bean并调用其实例方法
@Component
public class SpringBeanHolder implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        SpringBeanHolder.applicationContext = applicationContext;
    }

    public static <T> T getBean(Class<T> clazz) {
        return applicationContext.getBean(clazz);
    }

    public static <T> T getBean(String name, Class<T> clazz) {
        return applicationContext.getBean(name, clazz);
    }
}

// 这里就不用声明为Spring Bean了
public final class JacksonUtil {
    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return SpringBeanHolder.getBean(ObjectMapper.class).writeValueAsString(value);
    }
}

// 或者将SpringBeanHolder.getBean提取出来放到一个方法里
public final class JacksonUtil {
    private static volatile ObjectMapper objectMapper;

    private static ObjectMapper getObjectMapper() {
        if (Objects.isNull(objectMapper)) {
            synchronized (JacksonUtil.class) {
                if (Objects.isNull(objectMapper)) {
                    objectMapper = SpringBeanHolder.getBean(ObjectMapper.class);
                }
            }
        }
        return objectMapper;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return getObjectMapper().writeValueAsString(value);
    }
}

// 方法2，实例字段注入 + @PostConstruct赋值给static实例，注入的Spring Bean是final的，推荐
@Component
public final class JacksonUtil {
    private static JacksonUtil instance;

    private final ObjectMapper OBJECT_MAPPER;

    // 作为Spring管理的工具类，定义为包级构造器更合适
    JacksonUtil(ObjectMapper objectMapper) {
        this.OBJECT_MAPPER = objectMapper;
    }

    @PostConstruct
    public void init() {
        JacksonUtil.instance = this;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.readValue(content, valueTypeRef);
    }
}

// 方法3，实例字段注入 + @PostConstruct赋值给static字段，注入的Spring Bean不是final的，不推荐
@Component
public final class JacksonUtil {
    private static ObjectMapper objectMapper;

    private final ObjectMapper INJECTED_OBJECT_MAPPER;

    // 作为Spring管理的工具类，定义为包级构造器更合适
    JacksonUtil(ObjectMapper objectMapper) {
        this.INJECTED_OBJECT_MAPPER = objectMapper;
    }

    @PostConstruct
    public void init() {
        JacksonUtil.objectMapper = this.INJECTED_OBJECT_MAPPER;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return objectMapper.writeValueAsString(value);
    }
}
```

#### @AutoWired

- 其required属性默认为true，这时如果匹配失败，则会报错；改为false，这时如果匹配失败，则注入的值为null，会造成其所注解的成员变量为null导致的空指针异常

- 工作流程：根据类型查找Spring容器中的Bean
    - 找不到类型匹配的Bean，报错
    - 找到唯一类型匹配的Bean，注入
    - 找到多个类型匹配的Bean
    - 有@Qualifier注解，根据@Qualifier指定的名称作为Bean的id进行匹配
        - 找到，注入
        - 找不到，报错
    - 无@Qualifier注解，根据@AutoWired所注解的成员变量名作为Bean的id进行匹配
        - 找到，注入
        - 找不到，报错

```java
@Controller
public class XxxController {
    // 使用场景1，在成员变量上
    @AutoWired
    private XxxService xxxService;
}

@Controller
public class XxxController {
    private XxxService xxxService;

    // 使用场景2，在构造方法上
    // 在只有一个构造器的情况下，Spring会自动把它当成需要注入的构造器,写不写@Autowired都可以
    @AutoWired
    public XxxController(XxxService xxxService) {
        this.xxxService = xxxService;
    }
}

@Controller
public class XxxController {
    private XxxService xxxService;

    // 使用场景3，在set方法上
    @AutoWired
    public void setXxxService(XxxService xxxService) {
        this.xxxService = xxxService;
    }
}
```

#### @Qualifier

- 在按照类型注入的基础上（有多个匹配的实例），再按照名称注入

- 在给类成员注入时需要和`@Autowired`一起使用

- 在给方法参数注入时可以单独使用，用于该参数类型有多个实例的情况

```java
@Configuration
public class ApplicationConfiguration {
    @Bean(name = "queryRunner")
    public QueryRunner queryRunner(@Qualifier("dataSource2") DataSource dataSource) {
        return new QueryRunner(dataSource);
    }
}
```

#### @Primary

解决同一类型存在多个Bean实例的注入问题

在Bean定义时（例如使用 @Bean 或类注解）添加 @Primary 注解

表示该 Bean 是首选的注入对象。

当进行 @Autowired 注入时，如果没有使用 @Qualifier 指定名称，Spring 将优先选择带有 @Primary 的 Bean

```java
// 将 UserServiceImpl1 设为首选注入对象
@Primary 
@Service
public class UserServiceImpl1 implements UserService {}

@Service
public class UserServiceImpl2 implements UserService {}

@Controller
public class UserController {
    // 自动注入 UserServiceImpl1
    @AutoWired
    private UserService userService;
}
```

#### @Resource

- jdk11以上或jdk8以下版本需要导入依赖

```xml
<dependency>
    <groupId>jakarta.annotation</groupId>
    <artifactId>jakarta.annotation-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

- 根据名称进行匹配

- 如果没有指定name属性，先根据所注解的成员变量名查找Spring容器中的Bean
    - 没有对应的Bean，会再根据类型进行查找

- 只能注解在字段和Setter方法上，不支持构造器注入

```java
@Controller
public class XxxController {
    @Resource(name = "xxxService")
    private XxxService xxxService;
}
```

#### @Value

- @AutoWired、@Qualifier和@Resource都只能注入其他bean类型的数据，而@Value用来注入基本类型和String类型的数据

- 此注解所在的类必须是一个组件

- 作用在字段上，可以使用Spring的spel表达式，也可以用来注入配置文件中的属性值

- 只能读取单个值，不能读取集合

- 假设配置文件application.yaml存在配置

```yaml
user01:
    name: 张三
```

- 则应该这样注入

```java
@Component
public class UserDO {
    // 使用场景1
    @Value("${user01.name:默认值")
    private String name;
}

@Configuration
@PropertySource(value = "classpath:person.yaml")
public class ApplicationConfiguration {
    // 使用场景2
    @Bean
    public Person person(@Value("${user01.name:默认值") private String name) {
        Person person = new Person();
        person.setName(name);
        return person
    }
}
```

### 周期方法注解

周期方法必须为公有方法，无参、无返回：`public void xxx()`

- @PostConstruct，指定初始化方法

```java
@PostConstruct
public void init() {}
```

- @PreDestroy，指定销毁方法

```java
@PreDestroy
public void destroy() {}
```

- 也可以通过Bean注解的属性指定初始化方法和销毁方法

```java
@Configuration
public class ApplicationContextConfiguration {
    @Bean(name = "druidDataSource", initMethod = "init", destroyMethod = "destroy")
    public DruidDataSource dataSource() {
        return new DruidDataSource();
    }
}
```

### 作用域注解

- @Scope，指定bean的作用域，值SCOPE_SINGLETON、SCOPE_PROTOTYPE，默认单例

```java
// 使用场景1
@Scope(scopeName = ConfigurableBeanFactory.SCOPE_SINGLETON)
@Component
public void xxxClass() {}

// 使用场景2
@Configuration
public class ApplicationConfiguration {
    @Bean(name = "queryRunner")
    @Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
    public QueryRunner queryRunner(DataSource dataSource) {
        return new QueryRunner(dataSource);
    }
}
```

### xml方式配置Bean(了解)

- Account.java

```java
@Getter
@Setter
@ToString
public class Account {
    private Integer id;

    private String name;
}
```

- spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="account" class="com.handle.application.entity.Account">
        <property name="id" value="001"></property>
        <property name="name" value="handle"></property>
    </bean>

    <!-- 定义bean的别名 -->
    <alias name="account" alias="account1" />
</beans>
```

- test

```java
@Test
public void test() {
    ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
    ApplicationContext context2 = new FileSystemXmlApplicationContext("...\\spring.xml");
    ApplicationContext context3 = new AnnotationConfigApplicationContext(ApplicationConfiguration.class);
    Account a = context.getBean("account1", Account.class);
    log.info(a.toString());
}
```

#### spring对bean的管理

- 创建 bean 的三种方式

```xml
    <!-- 1）使用默认构造函数创建：bean标签里面只有id 和 class 属性，如果类没有默认构造函数，则无法创建对象 -->
    <bean id="accountService" class="com.handle.learn.service.AccountServiceImpl"></bean>

    <!-- 2）使用某个类（普通工厂）中的方法创建对象，并存入spring容器 -->
    <bean id="instanceFactory" class="com.handle.learn.factory.InstanceFactory"></bean>
    <bean id="accountService2" factory-bean="instanceFactory" factory-method="getAccountService"></bean>

    <!-- 3）使用某个类（普通工厂）中的静态方法创建对象，并存入spring容器 -->
    <bean id="accountService3" class="com.handle.learn.factory.StaticFactory" factory-method="getAccountService"></bean>
```

- bean 对象的作用范围

```xml
<!-- bean标签的 scope 属性指定 bean 的作用范围，取值有：
    1）singleton：单例（默认）
    2）prototype：多例
    3）request：作用于web应用的请求范围
    4）session：作用于web应用的会话范围
    5）global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，相当于session
-->
<bean id="accountService4" class="com.handle.learn.service.AccountServiceImpl" scope="singleton"></bean>
```

- bean 对象的生命周期

```xml
<!--
    1）单例对象
        出生：当容器创建时出生
        活着：只要容器还在，对象一直存活
        死亡：容器销毁，对象死亡
        总结：单例对象的生命周期和容器相同
    2）多例对象
        出生：使用对象时spring框架才开始创建
        活着：对象只要是在使用过程中就一直活着
        死亡：当对象长时间不用，且没有别的对象引用时，由java的垃圾收集器回收
-->
<bean id="accountService5" class="com.handle.learn.service.AccountServiceImpl" scope="singleton" init-method="init" destroy-method="destroy"></bean>
```

- spring 的依赖注入（Dependency Injection）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!-- IOC 的作用：降低程序间的耦合（依赖关系）
            依赖关系的管理：交给 Spring 来维护
            当前类需要用到其他类的对象，由 Spring 提供，我们只需在配置文件中说明
            依赖关系的维护：
                就称之为依赖注入
            能注入的数据有三类：
                1）基本数据类型和String
                2）其他bean类型（在配置文件中或者注解配置过的bean）
                3）复杂类型/集合类型
            注入的方式有三种：
                1）使用构造函数注入
                2）使用set方法注入（常用）
                3）使用注解注入
    -->

    <!-- 1）使用构造函数注入 
            使用标签：constructor-arg，标签在bean标签内部
            标签constructor-arg的属性：
                1）type：指定数据类型，该类型是构造函数中某个或某些参数的类型
                2）index：指定给构造函数中指定索引位置的参数赋值，索引从0开始
                3）name：指定给构造函数中指定名称的参数赋值（最常用）
                以上三个属性用于给指定构造函数中的指定参数赋值
                4）value：指定基本数据类型和String类型的数据
                5）ref：指定其他bean类型数据（在spring的ioc核心容器中出现过的bean对象）
            优点：在获取bean对象时，注入数据是必须的操作，否则对象无法创建
            缺点：改变了bean对象的实例化方式，使得在创建对象时，如果用不到这些数据也必须提供
    -->
    <bean id="accountService6" class="com.handle.learn.service.AccountServiceImpl">
        <constructor-arg name="parameterName1" value="123456"></constructor-arg>
        <constructor-arg name="parameterName2" ref="now"></constructor-arg>
    </bean>
    <bean id="now" class="java.util.Date"></bean>

    <!-- 2）使用set方法注入 
            涉及的标签：property，标签在bean标签内部
            标签property的属性：
                1）name：指定注入时调用的set方法名称（不是类属性名称）
                2）value：指定基本数据类型和String类型的数据
                3）ref：指定其他bean类型数据（在spring的ioc核心容器中出现过的bean对象）
            优点：创建对象没有明确限制，可以直接使用默认构造函数
            缺点：如果有某个成员必须有值，则获取对象时有可能set方法没有执行
    -->
    <bean id="accountService6" class="com.handle.learn.service.AccountServiceImpl">
        <property name="parameterName1" value="123456"></property>
        <property name="parameterName2" ref="now"></property>
    </bean>
    <bean id="now" class="java.util.Date"></bean>

    <!-- 用于给list结构集合注入的标签有：list、array、set，这些标签可以互换 -->
    <!-- array的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myArray">
            <array>
                <value>a</value>
                <value>b</value>
            </array>
        </property>
    </bean>

    <!-- list的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myList">
            <list>
                <value>a</value>
                <value>b</value>
            </list>
        </property>
    </bean>

    <!-- set的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="mySet">
            <set>
                <value>a</value>
                <value>b</value>
            </set>
        </property>
    </bean>

    <!-- 用于给map结构集合注入的标签有：map、props，这些标签可以互换 -->
    <!-- map的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myMap">
            <map>
                <entry key="dog" value="1"></entry>
                <entry key="cat">
                    <value>2</value>
                </entry>
            </set>
        </property>
    </bean>

    <!-- property的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myPropertys">
            <props>
                <prop key="username">tomcat</prop>
            </props>
        </property>
    </bean>
</beans>

```

## AOP

- 依赖：spring-aop、spring-aspects、aspectjweaver

```xml
<!-- 使用spring的功能一般都会导入spring-context，里面包含了spring-aop，一般不单独导入spring-aop -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
<!-- spring-aspects，里面包含了aspectjweaver -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>${spring.version}</version>
</dependency>
```

### AOP相关术语

- JoinPoint连接点：被拦截的点（方法）

- PointCut切入点：被增强的点（方法），没被增强的只算连接点，不算切入点

- Advice通知/增强：拦截到连接点之后要做的事情，分为前置通知，后置通知，异常通知，最终通知，环绕通知

- Aspect切面：切入点和通知（引介）的结合

- Weaving织入：把通知应用到被代理对象从而创建新的代理对象的过程

- Introduction引介：一种特殊的通知，在不修改类代码的前提下，引介可以在运行期为类动态地添加一些方法或属性

- Target目标对象：被代理对象

- Proxy代理：一个类被AOP织入后，就产生一个代理类

### 切入点表达式写法

- execution(访问修饰符 返回类型 包名.类名.方法名(参数列表))

- 访问修饰符：public/private

- 返回类型：基本数据类型/引用类型/void

- 只有同时不考虑访问修饰符和返回类型，二者才可一起写成`*`

- 包名可以使用通配符
    - 单层模糊，一级写一个`*`：`*.handle.*.service.*`
    - 多层模糊：`com..impl`，
    - `..`不能在开头，错误写法：`..impl`；正确写法`*..impl`

- 类名和方法名也可以使用*来实现通配：`*Impl`

- 参数列表
    - 具体参数，基本数据类型直接写类型名，引用类型写全限定类名，如：int，java.lang.String
    - 模糊参数，参数列表还可以使用通配符表示任意类型，但是只匹配有参数的方法
    - 任意参数：`..`，但是无参数还是建议用具体写法`()`

- 全通配写法：`* *..*.*(..)`，实际开发不会这么写

- 实际开发中通常切入到业务层实现类下的所有方法：`* com.handle.application.service.impl.*.*(..)`

### 注解方式配置AOP

#### 切点表达式复用

#### 当前类复用

```java
// 1.定义一个public void的无参切点表达式方法
// 2.@Pointcut指定切点表达式
@Pointcut("execution(* com.handle.springAopDemo.service.impl.*.*(..))")
public void pointcut() {}

// 3.增强注解中引用：切点表达式方法名（必须加括号）
@Before("pointcut()")
public void before() {}
```

#### 全局复用

```java
public class ApplicationPointcut {
    // 1.定义一个public void的无参切点表达式方法
    // 2.@Pointcut指定切点表达式
    @Pointcut("execution(* com.handle.springAopDemo.service.impl.*.*(..))")
    public void pointcut() {}
}

public class LogAspect {
    // 3.增强注解中引用：全限定类名.切点表达式方法名（必须加括号）
    @Before("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public void before() {}
}
```

- 所有的增强方法，都可以根据需要添加参数JoinPoint，获取目标方法的信息

```java
@Before("pointcut()")
public void before(JoinPoint joinPoint) {
    String targetMethodName = joinPoint.getSignature().getName();
    System.out.println(targetMethodName);
}
```

- @AfterReturning的方法还可以获取目标方法的返回值

```java
// returning指定接收返回值的形参名
@AfterReturning(pointcut = "pointcut()", returning = "result")
public void afterReturning(JoinPoint joinPoint , Object result) {}
```

- @AfterThrowing的方法还可以获取目标方法的异常信息

```java
// throwing指定接收异常信息的形参名
@AfterThrowing(pointcut = "pointcut()", throwing = "t")
public void afterThrowing(Throwable t) {}
```

#### 配置切面类

```java
@Slf4j
// 表示当前类是一个切面类，里面可以定义各种通知
@Aspect
@Component
public class LogAspect {
    @Before("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public void before(JoinPoint joinPoint) {
        log.info("2.执行before");
    }

    // returning指定接收返回值的形参名
    @AfterReturning(pointcut = "com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()", returning = "result")
    public void afterReturning(Object result) {
        log.info("3.执行afterReturning");
    }

    // throwing指定接收异常信息的形参名
    @AfterThrowing(pointcut = "com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()", throwing = "t")
    public void afterThrowing(Throwable t) {
        log.info("3.执行afterThrowing");
    }

    @After("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public void after() {
        log.info("4.执行after");
    }

    /**
     * Spring框架提供了一个接口ProceedingJointPoint，该接口有一个proceed()方法，调用此方法相当于调用了切入点方法。
     * 该接口可以作为环绕通知的方法参数，在程序执行时，Spring框架会提供该接口的实现类以供使用。
     * Spring中的环绕通知，是Spring框架提供的一种可以在代码中手动控制增强方法何时执行的方式。
     */
    @Around("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object result = null;
        try {
            // 此处可以写前置通知代码
            log.info("1.执行aroundBefore");
            // 得到切入点方法执行所需参数
            Object[] args = joinPoint.getArgs();
            // 调用切入点方法
            result = joinPoint.proceed(args);
            // 此处可以写后置通知代码
            log.info("5.执行aroundAfterReturning");
        } catch (Throwable t) {
            // 此处可以写异常通知代码
            log.error("5.执行aroundThrowing");
            throw new RuntimeException(t);
        } finally {
            // 此处可以写最终通知代码
            log.info("6.执行aroundFinally");
        }
        return result;
    }
}
```

#### 支持注解配置（@Aspect声明的组件）

```java
// 跟xml配置方式的<aop:aspectj-autoproxy />一样
@EnableAspectJAutoProxy
public class ApplicationConfiguration {}
```

### xml方式配置AOP（了解）

- xml方式配置

```xml
<!-- 1.配置包含切入点（方法）的Service对象 -->
<bean id="accountService" class="com.handle.application.service.impl.AccountServiceImpl" />

<!-- 2.配置通知类Logger的对象 -->
<bean id="logger" class="com.handle.application.logger.Logger" />

<!-- 3.配置AOP -->
<aop:config>
    <!-- 7.aop:pointcut写在aop:config标签内，并且在所有aop:pointcut标签前，则变成所有切面使用-->
    <aop:pointcut id="pointcut02" 
        expression="execution(public void com.handle.application.service.impl.AccountServiceImpl.saveAccount())" />

    <!-- 4.配置切面，id为切面的唯一标识，ref为第二部配置的bean的id -->
    <aop:ascept id="logAdvice" ref="logger">
        <!-- 5.配置通知的类型为前置通知，并且建立通知方法和切入点（方法）的关联 -->
        <aop:before method="printLog" 
            pointcut="execution(public void com.handle.application.service.impl.AccountServiceImpl.saveAccount())" />

        <!-- 6.第二种方法配置切入点表达式 -->
        <aop:before method="printLog" pointcut-ref="pointcut01" />

        <!-- aop:pointcut写在aop:ascept标签内只能是该切面使用-->
        <aop:pointcut id="pointcut01" 
            expression="execution(public void com.handle.application.service.impl.AccountServiceImpl.saveAccount())" />
    </aop:ascept>
</aop:config>
```

### Spring 5/Spring 6切面执行顺序

- 1.@Around环绕前
    - 2.@Before
        - 3.切入点
        - 4.@AfterReturning/（异常时，执行@AfterThrowing）
    - 5.@After
- 6.@Around环绕后/（异常时，执行@Around方法的catch代码，然后执行@Around方法的finally代码）

### 切面优先级

```java
// 值越小，优先级越高，可以简单地把值当成执行顺序理解
@Order(1)
@Aspect
@Component
public class LogAspect {
}
```

## TX

- 不可重复读：一个事务读到另一个事务已经提交的update的数据，导致一个事务中多次查询结果不一致
- 幻读：一个事务读到另一个事务已经提交的insert的数据，导致一个事务中多次查询结果不一致

### 注解配置事务管理器

```java
// 1.开启事务注解的支持
@EnableTransactionManagement
@Configuration
public class TransactionManagerConfiguration {
    @Bean
    public TransactionManager transactionManager(DataSource dataSource) {
        // 2.定义基于连接池的事务管理器实现（内部进行事务的操作）
        return new DataSourceTransactionManager(dataSource);
    }
}

// 3.添加事务用@Transactional，注解在类上时，给该类的所有方法添加事务；注解在方法上时，给该方法添加事务，并且覆盖类上的注解定义。
@Transactional
@Service
public class AccountService {
    @Autowired
    private AccountDao accountDao;
 
    // 4.也可以定义在方法上
    // @Transactional
    public void updateAccount(Long id, String name, boolean gender) {
        accountDao.updateNameById(name, id);
        accountDao.updateGenderById(gender, id);
    }
}
```

### @Transactional的属性（事务的属性）

- readOnly，只读，默认false，当在类上添加事务注解时，如果该类有纯查询方法，可以在该查询方法上再次添加事务注解，设置为只读模式，提高效率

- timeout，事务超时时间（秒），默认-1（永不超时），超过时间会回滚事务和抛出异常

- rollbackFor，指定遇到什么异常才回滚，开发项目必须设置，一般设置为`Exception.class`，所有异常都回滚

- noRollbackFor，指定遇到什么异常不回滚（在回滚异常范围内，控制某个异常不回滚）

- isolation，事务隔离级别，根据使用场景设置，一般`Isolation.READ_COMMITTED`

- propagation，事务的传播行为（事务之间调用，如何影响子事务，事务的传播行为是设置到子事务的。如当一个事务里面包含另一个事务时）。
    - 增删改操作一般用默认的`Propagation.REQUIRED`，查询操作一般用`Propagation.SUPPORTS`
    - `Propagation.SUPPORTS`，如果父方法存在事务，则加入，否则自己以非事务方式执行
    - `Propagation.REQUIRED`，默认值，如果父方法有事务，加入；如果没有，就新建事务自己独立
    - `Propagation.REQUIRES_NEW`，无论父方法是否有事务，都新建事务自己独立
    - 在同一个类中，对注解@Transactional的方法调用，事务传播行为不会生效。因为Spring框架是使用代理模式实现事务机制的，但是同一个类中的方法调用不经过代理，而是通过对象方法的调用，不会被代理捕获，也就不产生事务传播行为的效果。

### xml配置事务管理器

```xml
<bean id="txManager" class="...TransactionManager">

<aop:config>
    <!-- 配置通用切入点表达式 -->
    <aop:pointcut id="pointcut01" expression="execution(* com.handle.service.impl.*.*(..))" />
    <aop:aspect id="txAdvice" ref="txManager">
        <!-- 配置前置通知：开启事务 -->
        <aop:before method="beginTransaction", pointcut-ref="pointcut01" />
        <!-- 配置后置通知：提交事务 -->
        <aop:after-returning method="commitTransaction", pointcut-ref="pointcut01" />
        <!-- 配置异常通知：回滚事务 -->
        <aop:before method="rollbackTransaction", pointcut-ref="pointcut01" />
        <!-- 配置最终通知：释放连接 -->
        <aop:before method="releaseTransaction", pointcut-ref="pointcut01" />
    </aop:aspect>
</aop:config>
```

### JdbcTemplate

- jdbcTemplate的增删改都是用update方法，不同的只有执行的sql

## Test

- 依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${spring.test.version}</version>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>${junit.jupiter.version}</version>
</dependency>
```

- 使用

```java
// 1.spring-test的注解，指定配置类，这个配置类可以不用写@Configuration
@SpringJUnitConfig(value = ApplicationConfiguration.class)
public class ApplicationTest {
    // 2.注入组件
    @Autowired
    private CalculatorService calculatorService;

    @Test
    public void test() {
        // 3.使用组件
        BigDecimal result = calculatorService.divide(new BigDecimal("2.2"), new BigDecimal("0.2"));
        System.out.println(result);
    }
}
```

## Spring Bean生命周期

- 反射创建bean实例
- bean属性赋值
- 初始化
    - 初始化前置处理：BeanPostProcessor接口的实现类对象调用postProcessBeforeInitialization()方法
    - 如果有定义@PostConstruct标记的方法，则执行
    - 如果实现了InitializingBean接口，则执行afterPropertiesSet()方法
    - 如果配置了initMethod()方法，则执行
    - 初始化后置处理：BeanPostProcessor接口的实现类对象调用postProcessAfterInitialization()方法
- 使用
- 销毁
    - 如果有定义@PreDestroy标记的方法，则执行
    - 如果实现了DisposableBean接口，则执行destroy()方法
    - 如果配置了destroyMethod()方法，则执行
