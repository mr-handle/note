# Redis

CAP：（Consistency）强一致性、（Availability）可用性、（Partition tolerance）分区容错性
CA：传统Oracle数据库
AP：大多数网站架构的选择
CP：Redis、Mongodb
BASE：基本可用（Basically Available）、软状态（Soft state）、最终一致（Eventually consistent）

## Redis安装

key命名：`表名:列名:主键名:主键值`

### 修改配置

- redis7.4配置文件：[redis.conf](/file/redis/redis.conf)，其它版本可以去源码仓库那里下载

```conf
# "bind 127.0.0.1 -::1"表示只能本机访问
# 监听所有网卡这样写：bind 0.0.0.0，它会监听包括127.0.0.1的所有网卡
# 远程连接需要指定本机（某个网卡）的ip地址
bind 127.0.0.1 10.0.2.15

# 开启保护模式，默认
protected-mode yes

# 指定redis监听接收连接请求的端口，默认
port 6379

# 客户端空闲了没有给redis发送消息命令也不断开连接，默认
timeout 0

# 60秒检查一次客户端是否存活，默认300
tcp-keepalive 60

# 守护进程（daemon）方式后台运行，不占用当前终端
# redis采用的是单进程多线程的模式
# 当daemonize设置成yes时，代表开启守护进程模式
# 在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程
# 当daemonize选项设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出
daemonize yes

# daemonize设置为yes时，redis将进程id写入到pidfile指定的文件中，默认
pidfile /var/run/redis_6379.pid

# 日志级别，默认
loglevel notice

# 日志文件
# 默认logfile ""，表示打印日志到标准输出，同时如果开启了daemonize, 日志将发送到/dev/null
# 如果是主机部署redis，这样指定：logfile "/var/log/redis.log"
# 如果是docker部署redis，这样指定：logfile ""，然后docker会自动收集日志
logfile ""

# 是否把日志输出到系统日志，默认注释
#syslog-enabled no

# 指定redis在系统日志里面的日志标志，默认注释
#syslog-ident redis

# 指定系统日志设备，值必须为 USER 或 LOCAL0-LOCAL7之一，默认注释
# syslog-facility local0

# redis默认有16个数据库
databases 16

# 3600 秒内至少1次写操作 → 触发 RDB
save 3600 1

# 300 秒内至少 100 次写操作 → 触发 RDB
save 300 100

# 60 秒内至少 10000 次写操作 → 触发 RDB
save 60 10000

# 定义快照文件名，默认
dbfilename dump.rdb

# 指定登录密码
requirepass 具体密码

# 开启AOF持久化
appendonly yes

# 定义只追加文件名，默认
appendfilename "appendonly.aof"

# 每秒写入只追加文件，默认
appendfsync everysec
```

### 离线安装

- 解压tar.gz文件
- 进入redis根目录
- make命令安装redis
- make install

- 进入redis安装目录

```sh
cd /usr/local/bin
```

- 指定配置文件启动redis服务器

```sh
redis-server /handle/data/redis/conf/redis.conf
```

- 查看redis有没有启动

```sh
ps -ef|grep redis
```

- 指定端口启动redis客户端

```sh
redis-cli -p 6379
```

- 测试redis数据库是否启动成功

```sh
ping
```

- 关闭redis服务器

```sh
# 在redis客户端中端中关闭服务器
shutdown nosave
```

- 退出redis客户端

```sh
exit
```

### docker-redis

- 下载redis镜像

```sh
docker pull redis:8.10.0
```

docker redis默认不主动加载配置文件的，redis服务会直接以硬编码的内置默认参数运行

如果需要自定义配置，去源码仓库下载默认配置文件redis.conf进行修改

配置文件的配置项不是一成不变的，如果更新版本的话要以新的默认配置文件为参考

```conf
# 1. 引入官方默认配置文件，必须为容器内的绝对路径，不能写相对路径
# 补充：docker redis不用引入默认配置文件，它自己自带默认参数运行的，只要将改动的内容写到redis.conf就行了
# include /usr/local/etc/redis/redis.conf

# 2.在下方写入需要覆盖的配置（后面的配置会顶掉上面引入的配置文件的相同配置）

# 监听所有网卡的地址
bind 0.0.0.0

# 由于Docker 容器的生命周期依赖于前台的主进程
# 如果设置为daemonize yes，redis 启动后会把自己变成后台进程
# 此时，前台的父进程会立即退出，Docker 会认为“主程序结束了，容器该销毁了”，于是容器瞬间退出
# 因此如果是docker版的redis，要设置为daemonize no
daemonize no

# 登录密码
requirepass yourpassword

# 快照（RDB）默认是开启，还要设置触发保存RDB的条件
# save 指令是“追加型”的，先清空默认配置文件的save配置
# save ""
# 3600 秒内至少 1 次写操作 → 触发 RDB
save 3600 1
# 300 秒内至少 100 次写操作 → 触发 RDB
save 300 100
# 60 秒内至少 10000 次写操作 → 触发 RDB
save 60 10000

# 只追加文件（AOF）需要手动开启
appendonly yes

# 设置只追加文件每秒同步一次
appendfsync everysec

# 只追加文件的目录名称
appenddirname "aof"
```

- compose.yaml

```yaml
redis:
    image: redis:8.10.0
    container_name: redis
    ports:
        - "6379:6379"
    volumes:
        - redisData:/data
        - /home/handle/data/redis:/usr/local/etc/redis
    # 使用自定义配置文件
    command: redis-server /usr/local/etc/redis/redis.conf
    networks:
        - dubhe-net
    restart: always
networks: 
    dubhe-net:
        name: dubhe-net
volumes: 
    redisData:
        name: redisData
```

- 启动

```sh
# 首次启动
docker compose -f path/to/compose.yaml up -d

# 然后将修改好的配置文件复制到/var/lib/docker/volumes/redis-conf/_data
cp /path/to/redis.conf /var/lib/docker/volumes/redis-conf/_data

# 然后删除容器，不然直接执行启动配置文件不生效的
docker compose -f mycompose.yaml down

# 最后再启动
docker compose -f mycompose.yaml up -d
```

- 连接redis客户端

```sh
# 方法1，执行后将进入容器环境，然后输入redis-cli并回车，进入redis客户端 
docker exec -it redis01 /bin/bash

# 方式2
docker exec -it redis01 redis-cli
```

- 5.测试redis

```sh
ping
```

## Redis Insight

Redis Insight是Redis官方推出的redis数据库管理工具

官网：<https://redis.io/insight/>

github：<https://github.com/redis/RedisInsight>

官网下载要填各种信息，建议直接去github下载

- 安装docker版本的redisinsight

```sh
docker pull redis/redisinsight:2.42
```

- compose.yaml

```yaml
redisinsight:
    image: redis/redisinsight:2.42
    container_name: redisinsight
    ports:
        - "5540:5540"
    volumes:
        - redisinsight-data:/data

volumes: 
    redisinsight-data:
        name: redisinsight-data
```

- 访问：<http://你的ip:5540/>

## 数据库操作命令

```sh
# 选择数据库，默认16个数据库，数据库id为0-15
select 0

# 查看当前数据库的key数量
dbsize

# 列出当前数据库的所有key
keys *

# 把k1从当前库移到指定数据库1
move k1 1

# 清空当前数据库
flushdb

# 清空所有数据库
flushall
```

## 通用操作命令

```sh
# k1是否存在，1存在，0不存在
exists k1

# 删除k1
del k1

# 设置k1的过期时间为5秒
expire k1 5

# 查看k1剩余多少秒过期，-1永不过期，-2已过期
ttl k1

# 获取k1的类型
type k1
```

## `String`操作命令

```sh
# 设置k1的值为v1
set k1 v1

# 当且仅当k1不存在时设置k1的值为v1
setnx k1 v1

# 当且仅当k1不存在时设置k1的过期时间和值
setnx k1 30 v1

set key value nx px timeout

# 当且仅当k1存在时设置k1的过期时间和值
setex k1 30 v1

# 获取k1的值
get k1

# 设置k1为新值，返回老值
set k1 v1 get

# 设置k1为新值，返回老值
getset k1 v1

# 设置一个或多个key的值
mset k1 v1 k2 v2

# 获取一个或多个key的值
mget k1 k2

# 从k1下标为1开始，用ab，覆盖原来对应下标的值
setrange k1 1 ab

# 获取k1的子串（闭区间）
getrange k1 0 -1

# k1的（数字）值增1
incr k1

# k1的值自增步长值
incrby k1 2

# k1的（数字）值减1
decr k1

# k1的值自减步长值
decrby k1 2

# k1的值末尾追加字符s
append k1 s

# 获取k1的值的（字符串）长度
strlen k1
```

## `List`操作命令

- 是一个双端链表的结构
- List的头部为左边，List的尾部在右边
- 可以通过`rpush/lpop`或者`lpush/rpop`实现队列
- 可以通过`rpush/rpop`或者`lpush/lpop`实现栈

```sh
# 在k1的尾部（右边）添加一个或多个元素
rpush k1 1 2 3

# 在k1的头部（左边）添加一个或多个元素
lpush k1 1 2 3

# 将k1索引0的元素值设置为dong，索引越界会报错
lset k1 0 dong

# 获取k1的元素个数
llen k1

# 获取k1 0-2之间的元素，闭区间，如果是取出所有值可以用lrange k1 0 -1
lrange k1 0 2

# 移除并返回k1最左边的元素
lpop k1

# 移除并返回k1最右边的元素
rpop k1

# 通过索引获取列表中的元素
lindex list1 1

# 删除3个值为value1的元素
lrem list1 3 value1

# 截取范围内的值再赋给key
ltrim list 0 2

# list1取出一个元素加到list2
rpoplpush list1 list2

# 在列表中的指定元素前/后插入一个新元素
linsert list1 before/after 元素 新元素
linsert list1 after zhang san
```

## `Hash`操作命令

```sh
# 设置一个或多个键值对到k1，相当于k1.put("f1", "v1");k1.put("f2", "v2");
hset k1 f1 v1 f2 v2

# 当且仅当k1中不存在字段f1时设置f1的值为v1
hsetnx k1 f1 v1

# 同时设置多个键值对到k1，相当于k1.put("f1", "v1");k1.put("f2", "v2");
hmset k1 f1 v1 f2 v2

# 获取k1中字段f1的值，相当于k1.get("f1")
hget k1 f1

# 从k1中获取多个字段的值
hmget k1 f1 f2

# 获取k1的所有键值对
hgetall k1

# 判断k1中是否存在字段f1，1存在，0不存在
hexists k1 f1

# 删除k1中一个或多个字段
hdel k1 f1 f2

# 获取k1的字段个数
hlen k1

# 获取k1所有字段
hkeys k1

# 获取k1所有字段的值
hvals k1

# k1的字段f1的值加-1
hincrby k1 f1 -1

# k1的字段f1的值加0.1
hincrbyfloat k1 f1 0.1

# k1不存在f1时设置值
hsetnx k1 f1 v1
```

## `Set`操作命令

```sh
# 向k1添加一个或多个元素
sadd k1 v1 v2

# 删除k1的v1元素
srem k1 v1

# 获取k1的所有元素
smembers k1

# 获取k1的元素个数
scard k1

# 判断v1是否在k1中，存在1，不存在0
sismember k1 v1

# 获取k1和k2的交集
sinter k1 k2

# k1和k2的交集保存在k3中
sinterstore k3 k1 k2

# 获取k1和k2的并集
sunion k1 k2

# k1和k2的并集保存在k3中
sunionstore k3 k1 k2

# 获取k1和k2的差集（k1中去掉k1和k2的交集）
sdiff k1 k2

# k1和k2的差集保存在k3中
sdiffstore k3 k1 k2

# 随机获取k1中的两个元素，可以用于允许重复中奖的抽奖场景
srandmember k1 2

# 随机获取并移除k1中的两个元素，可以用于不允许重复中奖的抽奖场景
spop k1 2

# 将set1中的v1元素移动到set2中
smove set1 set2 v1
```

## `Sorted Set`操作命令

```sh
# 向k1中添加元素并指定排序
zadd k1 1 v1 3 v3 2 v2

# 获取k1的元素个数
zcard k

# 获取k1中v2的排序值
zscore k1 v2

# 获取k1索引的0到2的元素（按score从低到高排序），zrange k1 0 -1获取所有
zrange k1 0 2

# 获取k1索引的0到2的元素（按score从高到低排序），zrange k1 0 -1获取所有
zrevrange k1

# 获取k1中v1的排名（按score从低到高排序）
zrank k1 v1

# 获取k1中v1的排名（按score从高到低排序）
zrevrank k1 v1
```

## ReJSON-RL

```sh
# 设置json值
json.set 

# 获取json值
json.get 

# 删除json值
json.del 

# 批量获取多个key的json值
json.mget

# 获取json值的类型
json.type

# 对json中的数值进行增量操作
json.numincrby

# 追加字符串
json.strappend

# 获取json的长度
json.strlen
```

## 其它

- 使用aof持久化

```conf
appendonly yes
```

- 修复aof文件

```sh
redis-check-aof --fix appendonly.aof
```

- 修复rdb文件

```sh
redis-check-rdb --fix dump.rdb
```

- redis事务

```sh
# 监测某个key的改动，一般用在开启事务（multi）之前
watch key

# 取消对所有key的监测，一般用在事务执行exec失败（因为事务期间某个key被其他模块加塞更改了）之后
# 然后再 watch key 重新开始事务，直到执行成功
unwatch

# 标记一个事务块的开始
multi

# 执行事务块内的命令
exec

# 取消执行事务块内的命令
discard
```

## 主从复制

- 查看数据库信息

```sh
info replication
```

- 给当前数据库设置主数据库

```sh
# 假设主数据库为127.0.0.1 6379
slaveof 127.0.0.1 6379
```

- 使当前数据库停止与其他数据库的同步，自己成为主数据库

```sh
slaveof no one
```

- 启动哨兵

```sh
redis-sentinel sentinel.conf 
```

## Jedis

官网：<https://github.com/redis/jedis>

Jedis是以性能和简便为目标进行设计的Redis的java客户端

命令行中操作redis的指令在jedis中都有对应的方法实现

- maven依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <!-- 8.0.0开始支持java25 -->
    <version>8.0.2</version>
</dependency>
```

- jedis连接与基本使用

```java
@Test
public void testJedis() {
    // Jedis jedis = new Jedis("localhost", 6379);
    // 指定用户名和密码，如果没有设置用户名，则默认用户名为default
    Jedis jedis = new Jedis("redis://用户名:密码@ip:port");

    Assertions.assertEquals("PONG", jedis.ping());

    jedis.set("test", "success");
    Assertions.assertEquals("success", jedis.get("test"));

    jedis.close();
}
```

- 事务提交/取消

```java
 @Test
public void testTransaction() {
    Jedis jedis = new Jedis("www.laodeli.top", 6379);

    // 开始事务
    Transaction transaction = jedis.multi();
    transaction.set("k1", "v1");
    transaction.set("k2", "v2");
    // 取消事务
    transaction.discard();

    Assertions.assertEquals(null, jedis.get("k1"));
    Assertions.assertEquals(null, jedis.get("k2"));

    // 开始事务
    transaction = jedis.multi();
    transaction.set("k3", "v3");
    transaction.set("k4", "v4");
    // 提交事务
    transaction.exec();

    Assertions.assertEquals("v3", jedis.get("k3"));
    Assertions.assertEquals("v4", jedis.get("k4"));

    jedis.close();
}
```

## lettuce

Lettuce是一个可扩展的线程安全的Redis客户端，用于同步、异步和响应式使用

但是Lettuce的版本更新没有Jedis跟进那么快

```xml
<dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <!-- 7.x版本兼容到8.4、java24 -->
    <version>7.6.0.RELEASE</version>
</dependency>
```

## Spring Data Redis

Jedis不支持master/replica

- maven依赖

```xml
<!-- 默认使用 Lettuce -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- 用于测试的依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-test</artifactId>
    <scope>test</scope>
</dependency>
```

- 配置

```properties
# 写法一：指定url就行了，当设置了url时，host, port, username 和 password属性会被忽略
spring.data.redis.url=redis://default:redis123@10.0.2.15:6379

# 写法二：分开设置host, port, username 和 password
#spring.data.redis.host=10.0.2.15
#spring.data.redis.port=6379
#spring.data.redis.username=default
#spring.data.redis.password=redis123

# 指定使用的数据库
spring.data.redis.database=0

# 配置自动装配的 RedisConnectionFactory 使用 SSL 和 redis 服务器通信
#spring.data.redis.ssl.enabled=true

# 自定义SSL信任材料可以在SSL bundle中配置，自动应用到RedisConnectionFactory中
#spring.data.redis.ssl.bundle=example
```

- 然后就可以使用自动配置的：RedisConnectionFactory, StringRedisTemplate 或 RedisTemplate 了

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Test
public void test() {
    stringRedisTemplate.opsForValue().set("greet", "hello world");
    System.out.println(stringRedisTemplate.opsForValue().get("greet"));
}
```

### 配置Lettuce连接器

## 创建LettuceConnectionFactory

```java
// 写法一：单redis服务器
@Configuration
public class AppConfig {
    @Bean
    public LettuceConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory(new RedisStandaloneConfiguration("server", 6379));
    }
}

// 写法二：使用LettuceClientConfigurationBuilder
@Bean
public LettuceConnectionFactory lettuceConnectionFactory() {
    LettuceClientConfiguration clientConfig = LettuceClientConfiguration.builder()
        .useSsl().and()
        .commandTimeout(Duration.ofSeconds(2))
        .shutdownTimeout(Duration.ZERO)
        .build();

    return new LettuceConnectionFactory(new RedisStandaloneConfiguration("localhost", 6379), clientConfig);
}

// 写法三：Unix domain sockets to communicate with Redis.Unix domain socket at /var/run/redis.sock
@Configuration
public class AppConfig {
    @Bean
    public LettuceConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory(new RedisSocketConfiguration("/var/run/redis.sock"));
    }
}

// 写法四：写到主服务器，从备份服务器读
@Configuration
public class WriteToMasterReadFromReplicaConfiguration {
    @Bean
    public LettuceConnectionFactory redisConnectionFactory() {
        LettuceClientConfiguration clientConfig = LettuceClientConfiguration.builder()
        .readFrom(REPLICA_PREFERRED)
        .build();

        RedisStandaloneConfiguration serverConfig = new RedisStandaloneConfiguration("server", 6379);

        return new LettuceConnectionFactory(serverConfig, clientConfig);
    }
}

// 写法五：redis sentinel配置
@Bean
public RedisConnectionFactory lettuceConnectionFactory() {
    RedisSentinelConfiguration sentinelConfig = new RedisSentinelConfiguration()
        .master("mymaster")
        .sentinel("127.0.0.1", 26379)
        .sentinel("127.0.0.1", 26380);
    return new LettuceConnectionFactory(sentinelConfig);
}
```

### 配置Jedis连接器

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

## 创建JedisConnectionFactory

```java
// 写法一：单redis服务器配置
@Configuration
public class RedisConfiguration {
    @Bean
    public JedisConnectionFactory redisConnectionFactory() {
        RedisStandaloneConfiguration config = new RedisStandaloneConfiguration("server", 6379);
        return new JedisConnectionFactory(config);
    }
}

// 写法二：redis sentinel配置
@Bean
public RedisConnectionFactory jedisConnectionFactory() {
    RedisSentinelConfiguration sentinelConfig = new RedisSentinelConfiguration()
        .master("mymaster")
        .sentinel("127.0.0.1", 26379)
        .sentinel("127.0.0.1", 26380);
    return new JedisConnectionFactory(sentinelConfig);
}
```

### RedisSentinelConfiguration

RedisSentinelConfiguration可以通过RedisSentinelConfiguration.of(PropertySource)进行配置

可配置属性如下

```properties
# 主节点名称
spring.redis.sentinel.master=
# 以英文逗号分隔的节点的host:port对
spring.redis.sentinel.nodes=
# 使用Redis Sentinel进行身份验证时使用的用户名（redis 6以上版本）
spring.redis.sentinel.username=
# 使用Redis Sentinel进行身份验证时使用的密码
spring.redis.sentinel.password=
# 与Redis节点进行身份验证时应用的用户名
spring.redis.sentinel.dataNode.username=
# 与Redis节点进行身份验证时应用的密码
spring.redis.sentinel.dataNode.password=
# 使用Redis数据节点进行身份验证时应用的数据库索引
spring.redis.sentinel.dataNode.database=
```

有时候需要与sentinel进行直接互动

`RedisConnectionFactory.getSentinelConnection()` 或 `RedisConnection.getSentinelCommands()` 可以访问配置的第一个活动哨兵

### RedisClusterConfiguration

RedisClusterConnection 是对RedisConnection的扩展

RedisClusterConnection也是通过RedisConnectionFactory创建，可以通过RedisClusterConfiguration进行配置

RedisClusterConfiguration 也可以通过 RedisClusterConfiguration.of(PropertySource)进行配置

可以配置如下属性

```properties
# 以英文逗号分隔的节点的host:port对
spring.redis.cluster.nodes=
# 允许的集群重定向次数
spring.redis.cluster.max-redirects=
```

```java
@Component
@ConfigurationProperties(prefix = "spring.redis.cluster")
@Getter
@Setter
public class ClusterConfigurationProperties {

    /*
    * spring.redis.cluster.nodes[0] = 127.0.0.1:7379
    * spring.redis.cluster.nodes[1] = 127.0.0.1:7380
    * ...
    */
    List<String> nodes;
}

@Configuration
public class AppConfig {

    /**
     * Type safe representation of application.properties
     */
    @Autowired ClusterConfigurationProperties clusterProperties;

    @Bean
    public RedisConnectionFactory connectionFactory() {
        return new LettuceConnectionFactory(
            new RedisClusterConfiguration(clusterProperties.getNodes()));
    }
}
```

### [Reactive]RedisTemplate

[Reactive]RedisTemplate提供高级别的交互（各种数据类型，并且自动做序列化/反序列化）

[Reactive]RedisConnection提供低级别交互（返回字节数组）

- 声明RedisTemplate bean

```java
@Configuration
class MyConfig {
    @Bean
    LettuceConnectionFactory connectionFactory() {
        return new LettuceConnectionFactory();
    }

    @Bean
    RedisTemplate<String, String> redisTemplate(RedisConnectionFactory connectionFactory) {

        RedisTemplate<String, String> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        return template;
    }
}
```

- 使用Redistemplate

```java
public class Example {
    // inject the actual operations
    @Autowired
    private RedisOperations<String, String> operations;

    // Spring 自动调用 redisTemplate.opsForList()
    // inject the template as ListOperations
    @Resource(name="redisTemplate")
    private ListOperations<String, String> listOps;

    public void addLink(String userId, URL url) {
        listOps.leftPush(userId, url.toExternalForm());
    }
}
```

### StringRedisTemplate

StringRedisTemplate专门处理字符串操作

对应的低级别交互用StringRedisConnection（实现了DefaultStringRedisConnection）

底层使用StringRedisSerializer序列化

- 声明StringRedisTemplate bean

```java
@Configuration
class RedisConfiguration {
    @Bean
    LettuceConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory();
    }

    @Bean
    StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {

        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```

- 使用StringRedisTemplate

```java
public class Example {
    @Autowired
    private StringRedisTemplate redisTemplate;

    public void addLink(String userId, URL url) {
        redisTemplate.opsForList().leftPush(userId, url.toExternalForm());
    }
}
```

### RedisCallback

RedisTemplate 和 StringRedisTemplate可以让你使用RedisCallback接口直接跟Redis对话

```java
public void useCallback() {
    redisOperations.execute(new RedisCallback<Object>() {
        public Object doInRedis(RedisConnection connection) throws DataAccessException {
        Long size = connection.dbSize();
        // Can cast to StringRedisConnection if using a StringRedisTemplate
        ((StringRedisConnection)connection).set("key", "value");
        }
    });
}
```

## 分布式锁

缓存击穿解决方案：加锁限制访问

- 1.青铜方案：setnx加锁，获取到锁的微服务才能查数据库

```sh
# 当且仅当k1不存在时，将k的值设置为v1
setnx k1 v1
```

缺陷：setnx 占锁成功，业务代码出现异常或者服务器宕机，没有执行删除锁的逻辑，就造成了死锁。

- 2.白银方案：setnx加锁同时设置过期时间
缺陷：因为占锁和设置过期时间是分两步执行的，所以如果在这两步之间发生了异常，则锁的过期时间根本就没有设置成功。

- 3.黄金方案：setnx加锁同时设置过期时间作为原子操作执行

```sh
# 设置某个 key 的值并设置多少毫秒或秒 过期

set <key> <value> PX <多少毫秒> NX
或
set <key> <value> EX <多少秒> NX
```

缺陷：用户A处理业务时间长导致时间到自动开锁了，用户B获取到锁后和用户A操作冲突，用户B操作完把A的锁解开了

- 4.铂金方案：给锁设置编号，只能开编号对应的锁

缺陷：获取锁的值和删除锁不是原子操作

- 5.钻石方案：查询锁和删除锁这两步作为原子指令操作

缺陷：Lua 脚本，非专业

- 6.王者方案：使用Redisson

```java
// 1.设置分布式锁
RLock lock = redisson.getLock("lock");
// 2.占用锁
lock.lock();
// 3.执行业务
...
// 4.释放锁
lock.unlock();
```
