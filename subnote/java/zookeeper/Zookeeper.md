# Zookeeper

## 安装Zookeeper单机版

- 修改zoo_sample.cfg为zoo.cfg

```sh
mv zoo_sample.cfg zoo.cfg
```

- 修改zoo.cfg配置

```conf
dataDir=/handle/data/zookeeper/data
```

- 启动zookeeper

```sh
# 启动zookeeper服务器
bin/zkServer.sh start

# 查看zookeeper状态
bin/zkServer.sh status

# 停止zookeeper
bin/zkServer.sh stop

# 启动zookeeper客户端
bin/zkCli.sh

# 退出客户端
quit
```

## 安装Zookeeper集群

- 在dataDir定义的目录下创建一个myid文件
    - 在文件中填写server编号（上下不要有空行，左右不要有空格）
- 修改配置

```conf
# server.id=serverip:port1:port2
# id是myid文件里面对应的编号
# serverip是服务器地址
# port1是fllower和leader交换信息的端口，如2888
# port2是选举端口，如3888
server.1=serverip1:port1:port2
server.2=serverip2:port1:port2
server.3=serverip3:port1:port2
```
