# PowerJob

## 部署PowerJob

正式环境server和worker一定要部署在同一个网段！

如果server和worker不是部署在同一个局域网（如worker在宿主机ide运行，server在虚拟机（nat）的docker运行）
则server需要添加jvm参数-e JVMOPTIONS="-Dpowerjob.network.external.address=localhost -Dpowerjob.network.external.port.http=10010"；
且worker需要添加jvm参数-Dpowerjob.network.external.address=192.168.56.1 -Dpowerjob.network.external.port=27777，其中192.168.56.1为nat模式下virtualbox虚拟网卡分配的宿主机ip，千万不要用因特网网卡的ip

- 创建数据库

```sql
-- 根据部署的环境（日常（daily）、预发（pre）和线上（product））
-- 分别创建对应的数据库powerjob-daily、powerjob-pre 和 powerjob-product
create database powerjob-daily;
create database powerjob-pre;
create database powerjob_product;
```

- 拉取powerjob的docker镜像(由于server和worker需要在同一局域网部署，镜像的方式有很多问题，笔者这里也是部署到最后worker卡在等待worker接收这一步，推荐自己打包server然后作为服务器和worker放在同一个局域网)

```sh
docker pull powerjob/powerjob-server:4.3.9
```

- 启动调度服务器powerjob-server

```sh
# 这里用的是product环境启动，所以前面的步骤要创建好powerjob_product数据库
# 如果powerjob-server和数据库在同一个虚拟系统上，则连接时直接用虚拟系统的ip+数据库端口连接
docker run -d \
    --restart=always \
    --name powerjob-server01 \
    -p 7700:7700 -p 10086:10086 -p 10010:10010 \
    -e TZ="Asia/Shanghai" \
    -e JVMOPTIONS="-Dpowerjob.network.external.address=localhost -Dpowerjob.network.external.port.http=10010" \
    -e PARAMS="--spring.profiles.active=product --spring.datasource.core.driver-class-name=org.postgresql.Driver \
    --spring.datasource.core.jdbc-url=jdbc:postgresql://10.0.2.15:5432/powerjob_product \
    --spring.datasource.core.username=postgres --spring.datasource.core.password=postgres123" \
    -v /data/powerjob/powerjob-server:/root/powerjob/server -v /data/powerjob/.m2:/root/.m2 \
    powerjob/powerjob-server:4.3.9
```

- 检查是否启动成功

```sh
# 打开链接http://ip:${server.port:7700} 检验是否部署成功
# 不成功则查看日志
tail -f /data/powerjob/powerjob-server/logs/powerjob-server-application.log
```

- 查看账号密码(5.x)

```sh
# 搜索关键字命令1
cat /data/powerjob/powerjob-server/logs/powerjob-server-application.log | grep "username"

# 搜索关键字命令2
grep 'administrator' /data/powerjob/powerjob-server/logs/powerjob-server-application.log
```
