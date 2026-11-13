# Elasticsearch

- 官网：<https://www.elastic.co/cn/elasticsearch>

Elastic Stack（ELK Stack）：包括Elasticsearch、Kibana、Beats和Logstash，它们能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化

其中Elasticsearch是一个开源的高扩展的分布式全文搜索引擎，是整个ELK Stack的核心

## 核心概念

### 索引（Index）

一个索引就是一个拥有相似特征的文档的集合

### 文档（Document）

一个文档是一个可被索引的基础信息单元，也就是一条数据

文档以json格式来表示

### 字段（Field）

对文档数据根据不同属性进行的分类标识，相当于数据表的字段

### 映射（Mapping）

在处理数据的方式和规则方面做一些限制

如：某个字段的数据类型、默认值、分析器、是否被索引等，都可以在映射里面设置

至于其它处理ES数据的一些使用规则设置也叫做映射

### 分片（Shards）

一个索引可以存储超出单个节点硬件限制的大量数据，这可能导致任一节点都没有这样大的磁盘空间，或者导致单个节点处理搜索请求时，响应太慢

为了解决这个问题，ES提供了将索引划分成多份的能力，每一份就称之为分片（相当于关系型数据库的分表）

- 保存数据时，通过路由计算保存到哪个分片：hash(id) % 主分片数量
    - 客户端请求任意集群节点（协调节点）
    - 协调节点将请求转发到指定节点
    - 主分片将数据保存
    - 主分片将数据发送给副本
    - 副本保存后进行反馈
    - 主分片进行反馈
    - 客户端获取反馈

- 读取数据时，通过分片控制决定读取那个节点上的数据：可以访问任何一个节点获取数据，这个被访问的节点称为协调节点
    - 客户端请求任意集群节点（协调节点）
    - 协调节点计算数据所在分片及全部的副本位置
    - 协调节点将请求转发给具体的节点（负载均衡，轮询所有节点）
    - 节点返回查询结果，将结果反馈回客户端

### 副本（Replicas）

为了应对某个分片或节点发生故障的情况，需要有一个故障转移机制

为此ES允许创建分片的一份或多份拷贝，，这些拷贝就叫作复制分片或副本

### 分配（Allocation）

将分片分配给某个节点的过程，包括分配主分片或副本

如果是副本，还包含从主分片复制数据的过程，这个过程是由master节点完成的

一般分片数不超过节点数的3倍，可参考：节点数<=主分片数*(副本数+1)

### 评分机制

`得分 = boost * idf * tf`

boost：权重系数，默认2.2

#### TF（词频）

Term Frequency：搜索文本中的各个词条（term）在查询文本中出现了多少次，出现次数越多，就越相关，得分越高

`tf = freq / (freq + k1 * (1 - b + b * dl / avgdl))`

freq：关键词在当前文档中出现的次数

k1：关键词参数，默认1.2

b：关键词长度系数参数，默认0.75

dl：分词个数

avgdl：fields / documents，所有分词除以所有的文档数

#### IDF（逆文档频率）

Inverse Document Frequency：搜索文本中的各个词条（term）在整个索引的所有文档中出现了多少次，出现的次数越多，说明越不重要，也就越不相关，得分越低

`idf = log(1 + (N -n + 0.5) / (n + 0.5))`

log：e的对数

N：文档的总字段数

n：文档包含的词条数

## 安装elasticsearch

以elasticsearch 9.x为例

- 下载tar.gz文件并解压到指定目录

### 修改config/elasticsearch.yaml文件

#### 单点部署

```yaml
# 9.x版本指定ip和端口就可以进行访问了
network.host: 10.0.2.15
http.port: 9200

# 这一行是启动后动态生成的，开发阶段可以设置为false，就可以不用密码登录了
xpack.security.enabled: false
```

- 下面是网上的单点部署教程，笔者没试过，保留着

```yaml
# 集群名称
cluster.name: elasticsearch
# 节点名称
node.name: node-1
network.host: 10.0.2.15
http.port: 9200
# master节点名称，跟上面的节点名称一致
cluster.initial_master_nodes:["node-1"]

# 编辑/etc/security/limits.conf
# 在末尾添加如下内容，设置每个进程可以打开的文件数的限制
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536

# 编辑/etc/security/limits.d/20-nproc.conf
# 在末尾添加如下内容，设置每个进程可以打开的文件数的限制
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536
# 操作系统级别对每个用户创建的进程数的限制，"*"表示所有用户名
* hard nproc 4096

# 编辑/etc/sysctl.conf
# 在末尾添加如下内容，设置一个进程可以拥有的VMA（虚拟内存区域）的数量，默认65536
vm.max_map_count=655360

# 然后重新加载
sysctl -p
```

#### 集群部署

```yaml
# 集群名称
cluster.name: elasticsearch-cluster
# 节点名称，每个节点的名称不能重复
node.name: node-1
node.roles: [ master, data ]
# 节点主机名/ip，每个节点的主机名/ip不能重复
network.host: 10.0.2.15
# 节点端口
http.port: 9200
# 全新集群第一次启动时用来选举初始master的引导名单
# 初始化一个新的集群时需要此配置来选举master
# 第一次启动集群成功后立刻删掉，不然如果该机器宕机重启后将出现问题
# 貌似8.x后面部署集群不需要了
#cluster.initial_master_nodes:["node-1", "node-2", "node-3"]
# 节点发现，默认用9300端口通信
discovery.seed_hosts: ["10.0.2.15:9300", "10.0.2.16:9300", "10.0.2.17:9300"]
```

### 启动elasticsearch

- elasticsearch不允许使用root启动，先创建elasticsearch用户

```sh
# 创建elasticsearch用户，然后根据提示指定密码
useradd elasticsearch

# 设置密码
passwd elasticsearch

# 将elasticsearch目录和它的所有子目录/文件的权限赋给刚创建的用户
chown -R elasticsearch:elasticsearch /path/to/elasticsearch-xxx

# 切到elasticsearch用户
su elasticsearch
```

- 启动elasticsearch

```sh
# 先进入elasticsearch的bin目录
cd /path/to/elasticsearch-xxx/bin

# 启动elasticsearch
# 启动时，会动态生成文件，如果文件所属用户不匹配，会发生错误，因此前面需要先创建用户和设置目录权限
# -d：后台启动
# 第一次启动会打印elastic的密码和fingerprint，记得复制下来
./elasticsearch [-d]
```

- 测试，访问：<https://localhost:9200/>，输入用户名elastic，密码根据控制台打印的输入

- 修改密码

```sh
# -u：指定用户名
# -i：交互模式，如果不加此选项会进入自动生成密码的模式，无法自定义密码
sudo /path/to/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
```

- 找回fingerprint

```sh
# 证书在/path/to/elasticsearch/config/certs
openssl x509 -noout -fingerprint -sha256 -in /path/to/elasticsearch/config/certs/http_ca.crt | tr -d ':'
```

## Java Client

- 依赖

```xml
<dependency>
    <groupId>co.elastic.clients</groupId>
    <artifactId>elasticsearch-java</artifactId>
    <version>9.2.0</version>
</dependency>
```

- 配置

```java
@Configuration
public class ElasticsearchConfiguration {
    private String url = "https://ip:9200";

    private String username = "elastic";

    private String password = "...";

    private String fingerprint = "...";

    @Bean
    public ElasticsearchClient elasticsearchClient() {
        SSLContext sslContext = TransportUtils
                .sslContextFromCaFingerprint(fingerprint);

        return ElasticsearchClient.of(b -> b
                .host(url)
                .usernameAndPassword(username, password)
                .sslContext(sslContext)
        );
    }
}
```

- 使用

```java
// 创建index
CreateIndexResponse createIndexResponse = elasticsearchClient.indices().create(c -> c.index("test-index"));
System.out.println(createIndexResponse.acknowledged());

// 查询index
GetIndexResponse getIndexResponse = elasticsearchClient.indices().get(c -> c.index("test-index"));
System.out.println(getIndexResponse.toString());
```

## SpringData集成

官网：<https://spring.io/projects/spring-data-elasticsearch>

## Kibana

Kibana 是一个用户界面，让你对es数据进行可视化的各种操作

下载：<https://www.elastic.co/cn/downloads/kibana>，然后解压，以9.x版本为例

```sh
# 将kibana目录和它的所有子目录/文件的权限赋给elasticsearch
chown -R elasticsearch:elasticsearch /path/to/kibana-xxx
```

- 自动生成es的kibana_system用户密码

```sh
elasticsearch/bin/elasticsearch-reset-password -u kibana_system
```

- 编辑配置文件/path/to/kibana/config/kibana.yaml

```yaml
server.port: 5601
server.host: "ip"
# es节点的ip和端口
elasticsearch.hosts: ["https://ip:port"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "..."
elasticsearch.ssl.verificationMode: none
```

- 启动

```sh
su elasticsearch

/path/to/kibana/bin/kibana
```

- 访问<http://10.0.2.15:5601>，然后输入elastic和密码就可以了

## EQL

Event Query Language，事件查询语言，是一种基于事件的时间序列数据（如日志、指标和跟踪）的查询语言

要运行EQL搜索，搜索到的数据流或索引必须包含时间戳和事件类别字段

## ES的SQL

```json
// 查询所有索引
GET _sql?format=txt
{
    "query": """
        show tables
    """
}

// 查询指定索引
GET _sql?format=txt
{
    "query": """
        show tables like 'test%'
    """
}

// 查询索引结构，索引有特殊符号要用引号括住
GET _sql?format=txt
{
    "query": """
        describe "indexName"
    """
}

// 查询索引的文档数据
GET _sql?format=txt
{
    "query": """
        select * from "indexName" limit 5
    """
}

// rlike，r表示正则
GET _sql?format=txt
{
    "query": """
        select * from "indexName" where name rlike 'ha*le'
    """
}
```

### 游标（cursor）

游标是系统为用户开设的一个数据缓冲区，存储sql语句的执行结果

每个游标区都有一个名字，用户可以用sql语句逐一从游标中获取记录，并赋给主变量，交由主语言进一步处理

```json
// 第一次查询写法，查询结果中有超过1条数据时只显示一条并且末尾包含游标
GET _sql?format=json
{
    "query": """
        select * from "indexName"
    """,
    "fetch_size": 1
}
// 然后可以复制第一次查询结果里面的游标，执行后就可以继续查看剩余的查询结果
// 如果执行后无结果，说明数据已经读取完毕，再次执行会返回错误结果
GET _sql?format=json
{
    "cursor": ...
}

// 最后关闭缓冲区
POST _sql/close
{
    "cursor": ...
}
```
