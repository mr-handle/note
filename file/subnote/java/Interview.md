# 面试篇

## 异常问题

### catch捕获了异常，try中的return还会返回吗？

答：不返回！

例：

```java
@Slf4j
public class ExceptionTest {
    @Test
    public void test() {
        // 返回2
        log.info("result of fun: {}", fun());
    }

    private int fun() {
        try {
            int a = 1 / 0;
            return 1;
        } catch (Exception e) {
            log.error("", e);
        }
        return 2;
    }
}
```

## 序列化问题

### 为什么pojo类布尔类型字段不要用is开头？

答：因为不同框架、序列化工具对JavaBean规范的遵守不一样，造成对isFieldName这样的布尔类型字段的解析不一致

导致属性名、getter/setter名、序列化/反序列化字段名不一致，从而引发难以排查的兼容性问题

因为不同的序列化工具（如jackjson、gson）对于布尔类型字段isFieldName的处理不同，如

jackjson遵循JavaBeans规范，isFieldName被认为是一个`方法`，解析完得到的是fieldName

gson不遵循JavaBeans规范，isFieldName被认为是一个`字段`，解析完得到的是isFieldName

如果用jackjson序列化，而用gson反序列化，将导致布尔类型的字段值丢失

团队开发的时候，如微服务之间API调用，你根本不知道对方是用什么序列化/反序列化工具，因此pojo类布尔类型字段不要用is开头

- 定义一个遵循JavaBeans规范的VO

```java
// 如果字段是boolean类型的isFieldName，lombok生成的getter/setter是isFieldName()/setFieldName()
// 如果字段是Boolean类型的isFieldName，lombok生成的getter/setter是getIsFieldName()/setIsFieldName()
// 虽然Boolean没有这个不统一的问题，但是谁知道会使用到哪些框架，它们对isFieldName的解析统一与否，因此还是不要使用isFieldName来定义布尔字段
@ToString
public class UserVO {
    private Boolean isMale;

    public Boolean isMale() {
        return this.isMale;
    }

    public void setMale(Boolean male) {
        this.isMale = male;
    }
}
```

```java
UserVO userVO = new UserVO();
userVO.setMale(true);

String json = new ObjectMapper().writeValueAsString(userVO);
// {"male":true}
System.out.println(json);

Gson gson = new Gson();
// {"isMale":true}
System.out.println(gson.toJson(userVO));

// 用jackson序列化，用gson反序列化
UserVO userVOFromJson = gson.fromJson(json, UserVO.class);
// UserVO(isMale=null)，结果丢失了isMale的字段值
System.out.println(userVOFromJson);
```

## Spring问题

### Spring是如何解决循环依赖的？

答：使用三级缓存机制（只适用于单例且非构造器注入的场景）

如果A依赖B，B又依赖A，如果都在构造器里注入，直接就报错了

- 一级缓存（singletonObjects）：存成品Bean对象（完全初始化）
- 二级缓存（earlySingletonObjects）：存半成品Bean对象（实例化了但还没注入属性）
- 三级缓存（singletonFactories）：存Bean工厂（用于创建代理或提前暴露引用）

- 创建A时，将其工厂放进三级缓存

- 然后发现A依赖B，于是创建B

- 然后发现B需要A，
    - 先查一级缓存，没有
    - 再查二级缓存，没有
    - 再查三级缓存，找到了A的工厂
        - 调用工厂创建一个提前暴露的A的引用
        - 放入二级缓存
        - 从三级缓存移除A的工厂
        - 这样B就拿到了A的引用了（虽然A还没初始化）
- 然后B完成初始化，放入一级缓存
- 继续完成A的初始化，正常注入B
- 然后B完成初始化，放入一级缓存
- 最终成功解决循环依赖

```yaml
# Spring Boot 2.6+默认禁止循环依赖，需要手动开启才可以
spring:
    main:
        allow-circular-references: true
```
