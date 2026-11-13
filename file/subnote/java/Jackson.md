# Jackson

- @JsonIgnoreProperties 作用在类上用于过滤掉特定字段不返回或者不解析

```java
@Getter
@Setter
@ToString
@JsonIgnoreProperties({"userRoles"})
public class UserVO {
    private String userNo;

    private String userName;

    private List<UserRole> userRoles = new ArrayList<>();
}
```

- @JsonIgnore 作用于字段或getter/setter 方法级别，用于指定在序列化或反序列化时忽略该特定属性

```java
@Getter
@Setter
@ToString
public class UserVO {
    private String userNo;

    private String userName;

    @JsonIgnore 
    private List<UserRole> userRoles = new ArrayList<>();
}
```

- @JsonFormat 用于指定属性在序列化和反序列化时的格式。常用于日期时间类型的格式化。

```java
@Getter
@Setter
@ToString
public class UserVO {
    private String userNo;

    private String userName;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd 'T' HH:mm:ss", timezone = "GMT+8") 
    private LocalDateTime createTime;
}
```

- @JsonUnwrapped 注解作用于字段上，用于在序列化时将其嵌套对象的属性“提升”到当前对象的层级，反序列化时执行相反操作。这可以使 JSON 结构更扁平。

```java
@Getter
@Setter
@ToString
public class UserVO {
    private String userNo;

    private String userName;

    @JsonUnwrapped
    private PageInfo pageInfo;

    @Getter
    @Setter
    @ToString
    public static class PageInfo {
        private Integer currentPage;

        private Integer pageSize;
    }
}
```

```json
// 扁平化后的JSON结构为
{
  "userNo": "0101",
  "userName": "handle",
  "currentPage": "1",
  "pageSize": "15"
}
```

## 常规使用

- 依赖

```xml
<!-- jackson2 -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.22.2</version>
</dependency>

<!-- jackson3，操作不再强制处理异常，非常推荐 -->
<dependency>
    <groupId>tools.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>3.2.2</version>
</dependency>
```

- 使用

```java
@Getter
@Setter
@ToString
public class User {
    private Long id;

    private String name;

    // 也可以不用@JsonSerialize和@JsonDeserialize注解，在创建ObjectMapper时，注册一个新的JavaTimeModule，
    // ObjectMapper mapper = new ObjectMapper().registerModule(new
    // JavaTimeModule());
    @JsonFormat(pattern = "yyyy-MM-dd", timezone = "GMT+8")
    // @JsonSerialize(using = LocalDateSerializer.class)
    // @JsonDeserialize(using = LocalDateDeserializer.class)
    private LocalDate birthday;
}

/**
 * 每个测试方法执行前执行一次
 */
@BeforeEach
public void init() {
    user = new User();
    user.setId(1L);
    user.setName("张三");
}

@Test
public void testJavaObjectToJson() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper().registerModule(new JavaTimeModule());
    // 反序列化时忽略不存在的JavaBean属性:
    objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

    // javabean转json
    String jsoString = objectMapper.writeValueAsString(user);
    System.out.println("user:" + jsoString);

    // json转javabean
    User user2 = objectMapper.readValue(jsoString, User.class);
    System.out.println("user2:" + user2.toString());

    List<User> list = new ArrayList<>();
    list.add(user);
    // java集合转json
    String jsonListString = objectMapper.writeValueAsString(list);
    System.out.println("list:" + jsonListString);

    // json转java集合
    List<User> list2 = objectMapper.readValue(jsonListString, new TypeReference<List<User>>() {});
    System.out.println("list2:" + list2.toString());
}
```

- 自定义JsonSerializer和JsonDeserializer来定制序列化和反序列化

```java
@Getter
@Setter
@ToString
public class User {
    // ...

    // 指定使用自定义的反序列化类
    @JsonDeserialize(using = UuidDeserializer.class)
    private String uuid;
}

public class UuidDeserializer extends JsonDeserializer<String> {
    @Override
    public String deserialize(JsonParser p, DeserializationContext ctxt) throws IOException, JacksonException {
        String property = p.getValueAsString();
        if (Objects.isNull(property)) {
            return null;
        }
        return property.replace("-", "");
    }
}
```

### Spring Boot中使用

- 只要导入了web启动器，不用导入Jackson依赖

- 配置文件

```properties
spring.jackson.time-zone=GMT+8
# 设置Date类型的日期格式
spring.jackson.date-format=yyyy-MM-dd HH:mm:ss
# 返回时间戳给前端，只对Date类型生效
spring.jackson.serialization.write-dates-as-timestamps=true
```

- 使用jackson

```java
@Autowired
private ObjectMapper objectMapper;
```

- 自定义Jackson配置类：[JacksonConfiguration](/java/JacksonConfiguration.java)

- ObjectMapper是线程安全的，因此可以定义一个Jackson工具类，避免每次注入Jackson

```java
// 方法1
// 或者使用SpringBeanHolder，它对所有工具类都适用，然后在工具类里面获取Spring Bean并调用其实例方法
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
        return SpringBeanHolder.getCacheBean(ObjectMapper.class).writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return SpringBeanHolder.getCacheBean(ObjectMapper.class).readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return SpringBeanHolder.getCacheBean(ObjectMapper.class).readValue(content, valueTypeRef);
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

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return getObjectMapper().readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return getObjectMapper().readValue(content, valueTypeRef);
    }
}

// 方法2，推荐
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

// 方法3，objectMapper不是final的，不推荐
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

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return objectMapper.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return objectMapper.readValue(content, valueTypeRef);
    }
}
```

### 超过16位的Long类型的字段序列化

```java
@Getter
@Setter
@ToString
public class UserVo {
    // 对于Long类型的数值超过16位时，直接序列化传给前端js将会丢失精度，需要先转为String类型来让js接收
    // 反序列化不用特殊处理，Jackson会自动将前端传来的String类型数值转换为Long类型
    @JsonSerialize(using = ToStringSerializer.class)
    private Long id;

    // 基本类型也可以，但是强烈不建议这样写的
    @JsonSerialize(using = ToStringSerializer.class)
    private long id;
}
```
