# Mybatis

## 完全配置类整合

```java
@Configuration
public class MybatisConfiguration {
    // SqlSessionFactoryBean的getObject方法会创建sqlSessionFactory，将其加入到IOC容器
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();

        // 设置连接池
        sqlSessionFactoryBean.setDataSource(dataSource);

        Configuration configuration = new Configuration();
        configuration.setMapUnderscoreToCamelCase(true);
        configuration.setLogImpl(Slf4jImpl.class);
        configuration.setAutoMappingBehavior(AutoMappingBehavior.FULL);
        // 设置settings
        sqlSessionFactoryBean.setConfiguration(configuration);

        // 设置别名
        sqlSessionFactoryBean.setTypeAliasesPackage("com.handle.pojo");

        PageInterceptor pageInterceptor = new PageInterceptor();
        Properties properties = new Properties();
        properties.setProperty("helperDialect", "postgresql");
        pageInterceptor.setProperties(properties);
        // 添加插件，如PageHelper
        sqlSessionFactoryBean.addPlugins(pageInterceptor);

        return sqlSessionFactoryBean;
    }
    
    // mapper代理对象加入到IOC容器
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        // 指定mapper.java和mapper.xml所在的共同包
        mapperScannerConfigurer.setBasePackage("com.handle.mapper");
        return mapperScannerConfigurer;
    }
}
```

## 配置mapper包扫描

- 1.mapper.xml的名称和mapper.java的名称必须相同

- 2.mapper.xml的文件夹结构和mapper.java的包名一致

```xml
<mappers>
    <package name="com.handle.application.dao" />
</mappers>
```

## 列名和属性映射

### 1.开启驼峰式映射

```xml
<settings>
    <!-- account_id accountId 自动映射 -->
    <setting name="mapUnderscoreToCamelCase" value="true" />
</settings>
```

### 2.定义别名

```sql
select account_id as accountId from Account

-- 可以省略as
select account_id accountId from Account
```

### 3.resultMap自定义映射

```xml
<!-- 1.定义 -->
<resultMap id="AccountMap" type="com.handle.Application.po.Account">
    <!-- 1.1定义主键映射关系 -->
    <id column="account_id" property="accountId" />
    <!-- 1.2定义普通列映射关系 -->
    <result column="account_name" property="accountName"/>
</resultMap>

<!-- 2.使用 -->
<select id="queryAccountById" resultMap="AccountMap">
    select account_id from Account
</select>
```

## 表映射

```xml
<settings>
    <!-- 设置对于resultMap标签，无论有没有嵌套（association/collection）都自动映射result标签的列名和属性 -->
    <setting name="autoMappingBehavior" value="FULL" />
</settings>
```

### 一对一

从表实体包含主表实体的对象引用，一个账号对应一个用户

- 用户实体

```java
@Getter
@Setter
public class User {
    private Long userId;

    private String userName;
}
```

- 账户实体

```java
@Getter
@Setter
public class Account {
    private Long accountId;

    private Long userId;

    private BigDecimal money;

    // 一对一，属性中包含对方对象
    private User user;
}
```

```xml
<resultMap id="accountUserMap" type="com.handle.application.Account">
    <id column="account_id" property="accountId" />
    <result column="user_id" property="userId" />
    <result column="money" property="money" />
    <association column="user_id" property="user" javaType="com.handle.application.User">
        <id column="user_id" property="userId" />
        <result column="user_name" property="userName" />
    </association>
</resultMap>
```

### 一对多

主表实体包含从表实体的集合引用，一个用户有多个账户

- 用户实体

```java
@Getter
@Setter
public class User {
    private Long userId;

    private String userName;

    // 一对多，属性中包含对方对象集合
    private List<Account> accounts;
}
```

- 账户实体

```java
@Getter
@Setter
public class Account {
    private Long accountId;

    private Long userId;

    private BigDecimal money;
}
```

```xml
<resultMap id="userAccountMap" type="com.handle.application.User">
    <id column="user_id" property="userId" />
    <result column="user_name" property="userName" />
    <collection property="accounts" ofType="com.handle.application.Account">
        <id column="account_id" property="accountId" />
        <result column="user_id" property="userId" />
        <result column="money" property="money" />
    </collection>
</resultMap>
```

### 多对多

一个角色可以赋予给多个用户，一个用户可以拥有多个角色，这里只展示前者（跟一对多一样）

- 用户实体

```java
@Getter
@Setter
public class User {
    private Long userId;

    private String userName;
}
```

- 角色实体

```java
@Getter
@Setter
public class Role {
    private Long roleId;

    private String roleName;

    private List<User> users;
}
```

```xml
<resultMap id="someName" type="com.handle.application.Role">
    <id column="role_id" property="roleId" />
    <result column="role_name" property="roleName" />
    <collection property="users" ofType="com.handle.application.User">
        <id column="user_id" property="userId" />
        <result column="user_name" property="userName" />
    </collection>
</resultMap>
```

## 主键

### 获取插入数据的主键

- 自增长主键

```xml
<insert id="insertAccount" useGeneratedKeys="true" keyColumn="account_id" keyProperty="accountId">
    <!-- insert sql -->
</insert>
```

- 非自增长主键

```xml
<insert id="insertAccount">
    <selectKey order="BEFORE" resultType="java.lang.Integer" keyProperty="accountId">
        <!-- 查询下一个主键序列号的sql -->
    </selectKey>
    <!-- insert sql -->
</insert>
```

## 需要转义的符号

也可以用`<![CDATA[特殊符号]]>`，无需进行转义

|符号|原符号|替换符号|
|:-|:-|:-|
|小于|<|`&lt;`|
|小于等于|<=|`&lt;=`|
|大于|>|`&gt;`|
|大于等于|>=|`&gt;=`|
|不等于|<>|`&lt;&gt;`|
|与|&|`&amp;`|
|单引号|'|`&apos;`|
|双引号|"|`&quot;`|

## 动态SQL

### `if`标签

- 字符串判空

```xml
<if test="id != null and id != ''">
    and id = #{id}
</if>
```

- 字符串判等

```xml
<if test="name != null and name == 'handle'.toString()">
    and id = #{id}
</if>
```

- 集合判空

```xml
<if test="ids != null and ids.size() > 0">
    and id in
    <foreach collection="ids" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</if>
<if test="ids != null and !ids.isEmpty()">
    and id in
    <foreach collection="ids" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</if>
```

- 布尔值判断

```xml
<if test="true == deleteFlag">
</if>
```

### `foreach`标签

- collection，方法为单个List类型的参数时，如果不指定别名，则foreach的collection属性可填list或collection

- item，用来获取集合的每个元素

- open，遍历之前要添加的字符串

- separator，两次遍历之间添加的分隔符

- close，遍历之后要添加的字符串

```xml
<foreach collection="集合变量名称" item="item" index="index" open="(" separator="," close=")">
    #{item}
</foreach>
```

#### 批量插入写法

```xml
insert into account (account_name, gender) values
    <foreach collection="accounts" item="item" separator=",">
        (#{item.accountName}, #{item.gender})
    </foreach>
```

#### 批量更新写法

- 1.jdbcUrl设置允许执行多语句

```properties
jdbc.jdbcUrl=jdbc:postgresql://localhost:5432/handle?allowMultiQueries=true
```

- 2.编写sql

```xml
<foreach collection="accounts" item="item">
    update account 
    set account_name=#{item.accountName},gender=#{item.gender}
    where account_id = #{item.accountId};
</foreach>
```

### `where`标签

- 只有在一个以上的`if`条件满足的情况下才去插入`where`子句

- 自动去掉开头、末尾多余的`and`和`or`

```xml
<select id="findActiveBlogLike" resultType="Blog">
    select * from blog
    <where>
        <if test="state != null">
            state = #{state}
        </if>
        <if test="author != null and author.name != null">
            and author_name like #{author.name}
        </if>
    </where>
</select>
```

### `set`标签

- 用于动态包含需要更新的列
- 会动态前置`set`关键字
- 消除无关的逗号

```xml
<update id="updateAccount" parameterType="java.lang.String">
    update account
    <set>
        <if test="username != null">username=#{username},</if>
        <if test="password != null">password=#{password}</if>
    </set>
    where id=#{id}
</update>
```

### choose标签

```xml
<choose>  
    <when test="username != null and username != ''">  
        user_name=#{username} 
    </when >  
    <otherwise>
        user_name is null
    </otherwise>  
</choose>  

```

### sql标签

- 提取重复的sql片段

```xml
<!-- 1.定义 -->
<sql id="baseSql">
    select * from account
</sql>

<select id="queryAccountById">
    <!-- 2.引用 -->
    <include refid="baseSql" />
    where account_id=#{id}
</select>
```

### in 条件大于1000

- 待验证

```xml
(
    id in
    <foreach collection="ids" item="item" index="index" open="(" separator="," close=")">
        <if test="index % 999 == 998">) or id in (</if>
        #{item}
    </foreach>
)
```

- 更直观

```xml
(1, id) in
<foreach collection="ids" item="item" index="index" open="(" separator=',' close=")">
    (1, #{item})
</foreach>
```

## Mybatis中的延迟加载

一个用户对应多个账户，多个账号对应一个用户，一个账号对应一个用户

- 在查询用户时，用户下的账户信息什么时候使用才什么时候查询
- 在查询账户时，账户所属的用户信息应该随着账户查询时一起查询出来
- 延迟加载（懒加载）：在真正使用数据是才发起查询，不用的时候不查询（一对多、多对多表关系）
- 立即加载：不管用不用，只要一调用方法，马上发起查询（多对一、一对一表关系）

### 开启延迟加载

- SqlMapConfig.xml

```xml
<settings>
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```

- 一对一表关系开启延迟加载

- IUserDAO.xml

```xml
<mapper namespace="com.handle.dao.IUserDAO">
    <select id="queryUserById" resultType="com.handle.entity.User">
        select user_id as userId, user_name as userName from user where user_id=#{userId}
    </select>
</mapper>
```

- IAccountDAO.xml

`association`元素添加select标签

```xml
<mapper namespace="com.handle.dao.IAccountDAO">
    <resultMap id="accountUserMap" type="com.handle.entity.Account">
        <id property="accountId" column="account_id"/>
        <result property="userId" column="user_id"/>
        <result property="money" column="money"/>
        <association property="user" column="user_id" javaType="com.handle.entity.User" select="com.handle.dao.IAccountDao.queryUserById">
            <id property="userId" column="user_id"/>
            <result property="userName" column="user_name"/>
        </association>
    </resultMap>
</mapper>
```

- 一对多表关系开启延迟加载

- IAccountDAO.xml

```xml
<mapper namespace="com.handle.dao.IAccountDAO">
    <select id="queryAccountByUserId" resultType="com.handle.entity.Account">
        select account_id as accountId, user_id as userId, money from account where account_id=#{accountId}
    </select>
</mapper>
```

- IUserDAO.xml

`collection`元素添加select标签

```xml
<mapper namespace="com.handle.application.IUserDAO">
    <resultMap id="userAccountMap" type="com.handle.entity.User">
        <id property="userId" column="user_id"/>
        <result property="userName" column="user_name"/>
        <collection property="accounts" javaType="com.handle.entity.Account" select="com.handle.dao.IAccountDAO.queryAccountByUserId">
            <id property="accountId" column="account_id"/>
            <result property="userId" column="user_id"/>
            <result property="money" column="money"/>
        </collection>
    </resultMap>
</mapper>
```

## mybatis中的一级缓存和二级缓存

### 一级缓存

指的是SqlSession对象的缓存。当我们执行查询后，查询的结果会存入到SqlSession为我们提供的一块区域中。该区域是一个Map。当我们再次查询同样的数据，mybatis会先去SqlSession中查询是否有，有的话直接拿出来用。

- 一级缓存默认是开启的
- 当调用SqlSession的添加、修改、删除，提交，关闭等方法时，会清空一级缓存
- 也可以通过SqlSession的clearCache方法清空缓存。
- 当SqlSession对象消失时，一级缓存也不存在了。

### 二级缓存

指的是SqlSessionFactory对象的缓存。同一个SqlSessionFactory对象创建的SqlSession共享其缓存。二级缓存存放的是数据，不是（同一个结果集）对象

- 配置文件配置支持二级缓存，SqlMapConfig.xml

```xml
<settings>
    <setting name="cacheEnabled" value="true">
</settings>
```

- 配置当前映射文件支持二级缓存，mapper.xml

```xml
<mapper namespace="com.handle.application.IUserDAO">
    <cache/>
</mapper>
```

- 配置当前操作支持二级缓存

```xml
<mapper namespace="com.handle.application.IUserDAO">
    <select id="queryUserById" resultType="com.handle.entity.User" useCache="true>
        select user_id as userId, user_name as userName from user where user_id=#{userId}
    </select>
</mapper>
```

## `mybatis`注解开发

### 常规开发

```java
public interface IUserDAO {
    // 传统用as做字段-属性映射
    @Select("select user_id as userId, user_name as userName from user where user_id=#{userId}")
    User queryUserById(Long userId);

    @Select("select * from user where user_id=#{userId}")
    // mybatis支持@Results做字段-属性映射。如果Results里面的映射还想应用到别的方法，可以指定Results的id属性，然后在对应的方法上使用注解@ResultMap(value={"userMap"})
    @Results(id="userMap", value={
        @Result(id=true, property="userId", column="user_id"),
        @Result(id=false, property="userName", column="user_name")
    })
    User queryUserById(Long userId);

    @Select("select * from user limit 10")
    @ResultMap(value={"userMap"})
    List<User> queryUsers();

    @Insert("insert into user (user_id,user_name) values (#{userId}, #{userName})")
    int addUser(User user);

    @Update("update user set user_name = #{userName} where user_id=#{userId}")
    int updateUser(User user);

    @Delete("delete from user where user_id=#{userId}")
    int deleteUser(Long userId);
}
```

### 一对一表关系注解开发

- IUserDAO.java

```java
public interface IUserDAO {
    @Select("select user_id as userId, user_name as userName from user where user_id=#{userId}")
    User queryUserById(Long userId);
}
```

- IAccountDAO.java

```java
public interface IAccountDAO {
    @Select("select * from account")
    @Results(id = "accountMap", value = {
        @Result(id = true,  property = "accountId", column = "account_id"),
        @Result(id = false,  property = "userId", column = "user_id"),
        @Result(id = false,  property = "money", column = "money"),
        @Result(id = false,  property = "user", column = "user_id", 
        one = @One(select = "com.handle.dao.IUserDAO.queryUserById"), fetchType = FetchType.EAGER)
    })
    List<Account> queryAll();
}

```

### 一对多表关系注解开发

- IAccountDAO.java

```java
public interface IAccountDAO {
    @Select("select account_id as accountId, user_id as userId, money from account where user_id=#{userId}")
    List<Account> queryAccountByUserId(Long userId);
}
```

- IUserDAO.java

```java
public interface IUserDAO {
    @Select("select * from user")
    @Results(id = "userAccountMap", value = {
        @Result(id = true,  property = "userId", column = "user_id"),
        @Result(id = false,  property = "userName", column = "user_name"),
        // 在支持延迟加载的情况下fetchType = FetchType.LAZY可以延迟加载
        @Result(id = false,  property = "accounts", column = "user_id", 
            many = @Many(select = "com.handle.dao.IAccountDAO.queryAccountByUserId"), fetchType = FetchType.EAGER)
    })
    List<Account> queryAll();
}

```

### mybatis注解开启二级缓存

- 配置文件配置支持二级缓存，SqlMapConfig.xml

```xml
<settings>
    <setting name="cacheEnabled" value="true">
</settings>
```

- DAO添加`@CacheNamespace(blacking = true)`

```java
@CacheNamespace(blacking = true)
public interface IAccountDAO {
    @Select("select account_id as accountId, user_id as userId, money from account where user_id=#{userId}")
    List<Account> queryAccountByUserId(Long userId);
}
```

## `#{参数名}` 和 `${参数名}` 的区别

1) #{参数名}：结果等于参数值加‘’号，即‘参数值’，功能为PreparedStatement的参数占位符，防sql注入

2) ${参数名}：结果等于参数值，功能为Statement对象的字符串拼接SQL，不防sql注入

## 模糊查询

```xml
<select id="queryUserByName" parameterType="java.lang.String" resultType="User">
    select id, name from user where name like "%" #{name} "%"
</select>
```

## 插入数据后返回id

```xml
<!-- keyProperty为实体类属性名称，keyColumn为数据库字段名称，resultType为返回值类型，AFTER表示执行插入语句后，再执行获取id的操作 -->
<select id="addUser" parameterType="com.handle.application.domain.UserDO">
    <selectKey keyProperty="id" keyColumn="id" resultType="java.lang.Integer" order="AFTER">
        select last_insert_id()
    </selectKey>
    insert into user (id, name) values (null, #{name})
</select>
```

## DAO接口返回值

当DAO接口返回单个对象数据时，如果sql的结果为空，则返回值为null

```java
// 当没有用户id为userId的记录时，user为null
UserDO user = userDAO.queryUser(userId);
```

当DAO接口返回集合对象数据时，如果sql的结果为空，则返回长度为0的空集合

```java
// 当没有用户年龄为userAge的记录时，users不为null，其长度为0
List<UserDO> users = userDAO.queryUsers(userAge);
```

## Executor

- MyBatis 有三种基本的 Executor 执行器：
    - SimpleExecutor： 每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象。
    - ReuseExecutor： 执行 update 或 select，以 sql 作为 key 查找 Statement 对象，
        存在就使用，不存在就创建，用完后，不关闭 Statement 对象，而是放置于 Map<String, Statement>内，供下一次使用。简言之，就是重复使用 Statement 对象。
    - BatchExecutor：执行 update（没有 select，JDBC 批处理不支持 select），
        将所有 sql 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），
        它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理。与 JDBC 批处理相同。

作用范围：Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内。

在 MyBatis 配置文件中，可以指定默认的 ExecutorType 执行器类型，也可以手动给 DefaultSqlSessionFactory 的创建 SqlSession 的方法传递 ExecutorType 类型参数

## MyBatis插件

MyBatis 仅可以编写针对 ParameterHandler、 ResultSetHandler、 StatementHandler、 Executor 这 4 种接口的插件

MyBatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法

具体就是 InvocationHandler 的 invoke() 方法，当然，只会拦截那些你指定需要拦截的方法。

实现 MyBatis 的 Interceptor 接口并复写 intercept() 方法，然后再给插件编写注解，指定要拦截哪一个接口的哪些方法即可

最后在配置文件中配置你编写的插件

## IDEA插件

- MyBatisX

## PageHelper

官网：<https://github.com/pagehelper/Mybatis-PageHelper>

- 依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <!-- 23年最高版本，已经好久没更新，推荐使用mybatis-plus分页 -->
    <version>2.1.0</version>
</dependency>
```

- 配置

```properties
# 配置使用默认就行
pagehelper.propertyName=propertyValue
pagehelper.reasonable=false
pagehelper.defaultCount=true
```

- 使用

```java
PageHelper.startPage(pageNumber, pageSize);
// PageHelper.startPage后面必须紧接查询语句
List<UserDO> users = userDAO.queryUsers(userAge);

// 获取总数
long total = new PageInfo<>(users).getTotal();
```

## Mybatis-Plus

可以快速进行单表的CRUD

- 依赖，需要参考mybatis-spring-boot-starter的版本，避免版本冲突

```xml
<!--父项目-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-bom</artifactId>
            <version>3.5.17</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<!--子项目-->
<!-- spring-boot场景启动器 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-spring-boot4-starter</artifactId>
</dependency>
<!-- 插件依赖，配置插件需要用到 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-jsqlparser</artifactId>
</dependency>
```

- 配置

```conf
# mybatis-plus配置
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.slf4j.Slf4jImpl
```

- 配置类

```java
@Configuration
public class ApplicationConfiguration {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 乐观锁插件，在更新的时候比较版本字段，并且做版本自增
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        // 防止全表删除和更新的拦截器
        interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
        // 如果配置多个插件, 切记分页最后添加
            // 如果有多数据源可以不配具体类型, 否则都建议配上具体的 DbType
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
        return interceptor;
    }
}
```

- service写法，service层提供了批量数据的操作

```java
// IService提供了一半的方法
public interface AccountService extends IService<AccountPo> {}

// ServiceImpl提供了另一半的方法
public class AccountServiceImpl extends ServiceImpl<AccountDao, AccountPo> implements AccountService {}
```

- mapper写法，mapper层提供了单个数据的操作

```java
// BaseMapper指定的实体名要跟表名一样，否则实体的类上要加注解@TableName指定表名
public interface AccountDao extends BaseMapper<AccountPo> {}
```

### 分页

- 1.配置分页插件

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    // 如果配置多个插件, 切记分页最后添加
    // 如果有多数据源可以不配具体类型, 否则都建议配上具体的 DbType
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
    return interceptor;
}
```

- 2.使用mybatis-plus自带的查询方法分页

```java
// 2.1
Page<AccountPo> page = new Page<>(1, 5);
List<AccountPo> accounts = accountDao.selectList(page, null);
long total = page.getTotal();

// 2.1使用自定义的查询方法分页
// 这种分页方式的page参数可以为null
List<AccountPo> selectByGender(IPage<?> page, @Param("gender") boolean gender);

IPage<AccountPo> page = new Page<>(1, 5);
List<AccountPo> accounts = accountDao.selectByGender(page, false);
long total = page.getTotal();
```

- 3.使用自定义的查询方法分页

```java
// 这种返回类型为list的分页方式的page参数可以为null
List<AccountPo> selectByGender(IPage<?> page, @Param("gender") boolean gender);

IPage<AccountPo> page = new Page<>(1, 5);
List<AccountPo> accounts = accountDao.selectByGender(page, false);
long total = page.getTotal();
```

### 注解

- @TableName，指定实体类关联的表名，不加此注解的时候实体类名作为表名（忽略大小写）

- @TableId，当实体类的属性名和表的主键名不一样时，添加此注解进行关联，并且可以指定插入数据时的id分配算法（默认雪花算法，可以在配置文件统一设置id分配算法）

- @TableField，当实体类的属性名和表的列名不一样时，添加此注解进行关联
    - exist，当表没有这个字段时，必须设置为false

```java
@TableName("account")
public class AccountPo {
    @TableId("id")
    private Long id;

    @TableField("name")
    private String name;
}
```

### 逻辑删除

```properties
# 逻辑删除配置
mybatis-plus.global-config.db-config.logic-delete-field=deleted
mybatis-plus.global-config.db-config.logic-delete-value=true
mybatis-plus.global-config.db-config.logic-not-delete-value=false
```

### 乐观锁

- 1.配置拦截器

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
}
```

- 2.配置注解

```java
public class AccountPo {
    @Version
    private Integer version;
}
```

### 拦截器

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    // 乐观锁插件，在更新的时候比较版本字段，并且做版本自增
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    // 防止全表删除和更新的拦截器
    interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
    // 如果配置多个插件, 切记分页最后添加
        // 如果有多数据源可以不配具体类型, 否则都建议配上具体的 DbType
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
    return interceptor;
}
```

### 代码生成器

- 依赖

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>3.5.8</version>
    <scope>compile</scope>
</dependency>
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.8</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
</dependency>
```

- 生成代码

```java
String projectPath = "..\\mybatis-plus-generator";

FastAutoGenerator.create("jdbc:postgresql://localhost:5432/databaseName", "username", "password")
    // 全局配置
    .globalConfig(builder -> {
    // 设置作者
    builder.author("handle")
    // 指定java文件输出目录
    .outputDir(projectPath + "/src/main/java")
    .disableOpenDir()
    ;
    })
    // 包配置
    .packageConfig(builder ->
    // 设置父包名
    builder.parent("com.handle.mybatisPlusGenerator")
    // 设置实体类包名
    .entity("pojo.po")
    // 设置Service接口包名
    .service("service")
    // 设置Service实现类包名
    .serviceImpl("service.impl")
    // 设置Mapper接口包名
    .mapper("dao")
    // xml文件输出目录
    .pathInfo(Collections.singletonMap(OutputFile.xml, 
        projectPath + "/src/main/resources/mapper/com/handle/mybatisPlusGenerator/dao")) 
    )
    // 策略配置
    .strategyConfig(builder ->
    // 设置需要生成的表名，多张表用英文逗号隔开
    builder.addInclude("tableName")
        .entityBuilder()
        .disableSerialVersionUID()
        // 启用Lombok
        .enableLombok()
        // 启用字段注解
        .enableTableFieldAnnotation()
        .convertFileName(entityName -> entityName + "Po")
        .controllerBuilder()
        .disable()
        .serviceBuilder()
        .convertServiceFileName(entityName -> entityName + "Service")
        .mapperBuilder()
        .convertMapperFileName(entityName -> entityName + "Dao")
        .convertXmlFileName(entityName -> entityName + "Dao")
    )
    // 使用Freemarker引擎模板，默认的是Velocity引擎模板
    .templateEngine(new FreemarkerTemplateEngine())
    // 执行生成
    .execute();
```
