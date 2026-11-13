# 单元测试

## Junit5

- 依赖

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>${junit.version}</version>
    <scope>test</scope>
</dependency>
```

### Junit5常用注解

#### @BeforeEach/@AfterEach

可简单理解为在每个测试方法执行前/后执行

```java
@BeforeEach
public void init() {
    // todo
}
```

#### @BeforeAll/@AfterAll

可简单理解为在所有测试方法执行前/后执行，对于一个测试类只执行一次，并且必须注解在静态方法上

```java
@BeforeAll
public static void initStatic() {
    // todo
}
```

#### @ActiveProfiles

一般作用于测试类上， 用于声明生效的 Spring 配置文件

```java
// 指定在 RANDOM_PORT 上启动应用上下文，并激活 "test" profile
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test")
@Slf4j
public abstract class ApplicationTest {
    // todo
}
```

#### @WithMockUser

@WithMockUser 是 Spring Security Test 模块提供的注解，用于在测试期间模拟一个已认证的用户。

可以方便地指定用户名、密码、角色（authorities）等信息，从而测试受安全保护的端点或方法。

```java
// 指定在 RANDOM_PORT 上启动应用上下文，并激活 "test" profile
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test")
@Slf4j
public abstract class ApplicationTest {
    @Test
    // 测试数据将回滚
    @Transactional 
    // 模拟一个名为 "test-user"，拥有 TEACHER 角色和 read 权限的用户
    @WithMockUser(username = "test-user", authorities = { "ROLE_TEACHER", "read" }) 
    public void test() throws Exception {
        // ... 测试逻辑 ...
        // 这里可以调用需要 "ROLE_TEACHER" 权限的服务方法
    }
}
```

### Junit5执行顺序

- 1.@BeforeAll
- 2.循环以下步骤
    - 2.1 @BeforeEach
    - 2.2 @Test
    - 2.3 @AfterEach
- 3.@AfterAll

## Mock

- @Resource/@Autowired为真实调用

### @MockBean

- 如果没有制定规则，返回默认值（基本数据类型为0，对象为null）
- 如果制定了规则，就按照规则走

```java
when(someService.someMethod(parameter...)).thenReturn(someValue);
```

### SpyBean

如果制定了规则就按照规则走，没有制定规则就按照真实调用走
