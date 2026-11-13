# RocketMQ

中文官网：<https://rocketmq.apache.org/zh/>

## 启动

- 启动NameServer

```sh
## 启动namesrv
nohup sh bin/mqnamesrv &
 
## 验证namesrv是否启动成功
tail -f ~/logs/rocketmqlogs/namesrv.log
```

- 启动Broker+Proxy

NameServer成功启动后，我们启动Broker和Proxy，5.x 版本下我们建议使用 Local 模式部署，即 Broker 和 Proxy 同进程部署

```sh
## 先启动broker
nohup sh bin/mqbroker -n localhost:9876 --enable-proxy &

## 验证broker是否启动成功, 比如, broker的ip是192.168.1.2 然后名字是broker-a
tail -f ~/logs/rocketmqlogs/proxy.log 
```

## 关闭

```sh
sh bin/mqshutdown broker

sh bin/mqshutdown namesrv
```

- 通过mqadmin创建 Topic

```sh
sh bin/mqadmin updatetopic -n localhost:9876 -t TestTopic -c DefaultCluster
```

## 消息类型

Normal/FIFO/Delay/Transaction

系统默认的消息最大限制如下：

普通和顺序消息：4 MB

事务和定时或延时消息：64 KB
