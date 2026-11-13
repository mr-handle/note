# Docker

docker的基本组成：镜像、容器、仓库

## 安装docker

### Red Hat系列Linux安装Docker

```sh
# 如果安装过docker，先卸载旧版本的docker
sudo yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine


# 安装yum-utils，其提供了yum-config-manager
sudo yum install -y yum-utils

# 配置yum-config-manager，添加仓库地址，这里需要配置成国内仓库，比如阿里云，不要用官网默认的
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum软件包索引（可选操作）
sudo yum makecache fast 

# 安装docker引擎，如果提示接受GPG密钥，请验证指纹是否匹配060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35，如果是，则接受它
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 卸载docker引擎，但不会删除docker镜像、容器和配置文件，docker镜像、容器和配置文件需要手动删除
sudo yum remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras

# 删除docker镜像
sudo rm -rf /var/lib/docker

# 删除docker容器
sudo rm -rf /var/lib/containerd
```

### Ubuntu安装Docker

教程：<https://docs.docker.com/engine/install/ubuntu/>

- 卸载旧版本

```sh
sudo apt remove \
    $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
```

- 配置docker的apt仓库

```sh
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

- 安装

```sh
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 安装后一般docker会自己启动，查看命令
sudo systemctl status docker
```

### 将当前用户加入docker用户组

```sh
# 将当前用户加入docker用户组并立即生效
sudo usermod -aG docker $USER && newgrp docker
```

### 配置国内镜像源

- 1.配置文件`/etc/docker/daemon.json`中加入

```json
{
    "registry-mirrors": [
        "https://hub.docker.com"
    ]
}
```

- 2.重新启动docker

```sh
sudo systemctl daemon-reload

sudo systemctl restart docker
```

- 3.在命令行执行`docker info` ，如果从结果中看到了如下内容，说明配置成功:

```sh
Registry Mirrors:
    <https://docker.mirrors.ustc.edu.cn/>
```

### 启动docker

```sh
# 启动docker
sudo systemctl start docker

# 重启docker
sudo systemctl restart docker

# 关闭docker
sudo systemctl stop docker

# 查看docker状态信息
sudo systemctl status docker

# 将docker服务设置为开机启动
sudo systemctl enable docker
```

## docker常用命令

### 帮助命令

```sh
# docker版本
docker version

# docker概要信息
docker info

# docker总体帮助文档
docker help

# docker命令帮助文档
docker 具体命令 --help
```

### 镜像命令

- 列出本地主机所有镜像

```sh
# -a 列出本地所有镜像（含历史映像层），-q 只显示镜像id
docker images [-aq]
```

- 查询镜像

```sh
# 最好是去docker hub查询指定版本的详细信息，如镜像标签，只列出25个镜像（默认）
docker search [--limit 25] 镜像关键字 

docker search --limit 25 tomcat
```

- 拉取镜像

```sh
# 不指定标签会拉取最新镜像，相当于docker pull 镜像名:latest
docker pull 镜像名[:标签]

docker pull mysql:8.0.29
```

- 查看镜像/容器/数据卷所占的空间

```sh
docker system df
```

- 删除镜像

```sh
# -f 强行删除
docker rmi [-f] 镜像id或镜像名:标签

# 删除单个镜像
docker rmi -f mysql:8.0.29

# 删除多个镜像
docker rmi -f 镜像名1:标签 镜像名2:标签

# 删除全部镜像
docker rmi -f $(docker images -aq)
```

### 容器命令

- 新建容器并运行

```sh
# 需要考虑端口映射、配置映射、数据映射、环境变量、网络名等
# --name 自定义容器名
# -d 后台运行容器并返回容器id，也即启动守护式容器
# -i 以交互模式运行容器，通常与-t同时使用
# -t 为容器重新分配一个伪输入终端，通常与-i同时使用，-it即启动交互式容器（前台有伪终端，等待交互）
# -p 主机端口:容器端口
# -P 随机分配主机端口，很少用
docker run [选项] 镜像名:标签 [命令] [ARG...]

# 使用镜像centos:latest以交互模式新建一个容器并运行，在容器内执行/bin/bash命令
# 放在镜像名后的是命令，这里我们希望有个交互式shell，这里用的是/bin/bash
# 要退出终端，输入exit
docker run -it centos /bin/bash
```

- 设置容器开机/关闭自启动

```sh
docker update 容器id --restart always

docker update 容器id --restart no
```

- 启动容器(已停止运行的容器)

```sh
docker start 容器id/容器名
```

- 重启容器

```sh
docker restart 容器id/容器名
```

- 停止容器(正在运行的容器)

```sh
docker stop 容器id或容器名
```

- 强制停止容器

```sh
docker kill 容器id或容器名
```

- 展示容器列表（默认正在运行的容器）

```sh
# -a 列出所有的容器
# -l 显示最近创建的容器
# -n 任意正整数 显示任意个最近创建的容器
# -q 静默模式，只显示容器编号
docker ps [选项]
```

- 退出容器

```sh
# 在交互式伪终端，用exit退出，容器停止
exit

# 在交互式伪终端，按ctrl + p + q，容器不停止
ctrl + p + q
```

- 重新进入容器

```sh
# 推荐，在容器中打开新的命令终端，并且可以启动新的进程，用exit退出，不会导致容器停止，
docker exec -it 容器id /bin/bash

# 直接进入容器并启动命令终端，不会启动新的进程，用exit退出，会导致容器停止
docker attach 容器id
```

- 删除容器

```sh
# 单个删除已停止的容器，如果容器正在运行要加-f强制删除
docker rm [-f] 容器id

# 删除全部容器
docker rm -f $(docker ps -a -q)

# 删除全部容器
docker ps -a -q | xargs docker rm
```

- 查看容器日志

```sh
# -t显示时间
# -f 跟随最新的日志显示
# --tail 限制显示的日志行数
docker logs -t -f --tail 5 容器id
```

- 查看容器内运行的进程

```sh
docker top 容器id
```

- 查看容器内部细节

```sh
docker inspect 容器id
```

- 在容器内执行命令

```sh
# 在容器内执行命令 ls -l
docker exec -t 容器id [ls -l]
```

## docker的复制/加载

```sh
# 复制容器内的文件到linux主机
docker cp 容器id:容器内路径 主机路径

# 复制linux主机的文件到容器内
docker cp 主机路径 容器id:容器内路径

# 提交容器副本使之成为一个新的镜像
docker commit -m="描述信息" -a="作者" 容器id 要创建的目标镜像名:标签

# 容器备份为一个tar归档文件
docker export 容器id > 文件名.tar

# 将镜像备份出来
docker save my_tomcat:1.0 -o my-tomcat-1.0.tar

# 从容器备份文件创建一个新的文件系统再导入为镜像
cat 文件名.tar | docker import - 镜像用户/镜像名:标签

# 将.tar格式的镜像加载到Docker中
docker load -i my-tomcat-1.0.tar
```

## 搭建私服

- 拉取仓库镜像

```sh
docker pull registry:标签
```

- 运行仓库

```sh
# -v 主机目录:容器目录，默认仓库在容器/var/lib/registry目录下
docker run -d --name registry01 -p 5000:5000 --privileged=true -v /data/docker/registry:/tmp/registry  registry:标签
```

- 查看私服上的镜像

```sh
curl -XGET http://私服地址:端口/v2/_catalog
```

- 将镜像修改为符合私服规范的名称

```sh
docker tag 本地要推送的镜像名:标签 私服地址:端口/推送到私服后的镜像名:标签
```

- docker默认不允许通过http方式推送镜像，修改docker配置文件使之支持http，在文件原来的内容后面添加`,"insecure-registries": ["私服地址:端口"]`

```sh
vi /etc/docker/daemon.json
```

```json
{
    // daemon.json原来的内容后面加上这里表示为...，insecure-registries里面配置非https的hub的地址
    ...,
    "insecure-registries": ["私服地址:端口"]
}
```

- 然后重新启动docker

```sh
sudo systemctl daemon-reload

sudo systemctl restart docker
```

- 将镜像推送到私服

```sh
docker push 私服规范镜像名（私服地址:端口/镜像名:标签）
```

- 再次查看私服上的镜像

```sh
curl -XGET http://私服地址:端口/v2/_catalog
```

- 从私服上拉取镜像到本地

```sh
docker pull 私服规范镜像名（私服地址:端口/镜像名:标签）
```

## 容器数据卷

卷就是目录或文件，其设计目的就是数据的持久化，它独立于容器的生存周期，不会在容器删除时删除其挂载的数据卷。

docker挂载主机目录后加 `--privileged=true`，可以解决挂载目录没有权限的问题。该参数使得容器内的root拥有真正的root权限，否则，容器内的root只有普通用户权限

在用 docker run 命令的时候，使用 --mount 标记来将一个或多个数据卷挂载到容器里

还可以通过 --mount 标记将宿主机上的文件或目录挂载到容器中，这使得容器可以直接访问宿主机的文件系统

Docker 挂载主机目录的默认权限是读写，用户也可以通过增加 readonly 指定为只读

```sh
# 手动创建
docker volume create 卷名

# 自动创建，放在容器启动命令中
-v ngconf:/etc/nginx

# 删除卷
docker volume rm 卷名1 卷名2

# 查看所有的数据卷
docker volume ls

# 查看卷信息
docker volume inspect 卷名
```

- 对于容器卷，docker统一放在主机的`/var/lib/docker/volumes/卷名`目录下，需要有root权限才可以操作该目录

- 使用经验：对于容器数据用命名卷，对于经常改动的比如配置文件，用目录映射更好

```sh
# 目录映射（绑定挂载），可以自定义位置，但是必须先在主机上创建目录/文件
# 举个例子：映射的主机上目录是空的，容器里的目录非空，这种情况下，在主机上的该目录是看不到容器里面的内容的（被隐藏了）
# 因此，对于配置文件要提前复制一份到主机目录
# 一般情况下，比如说容器目录有config，里面有默认的配置文件了，如果想自定义配置，那么应该映射到容器的一个空目录中，比如config.d，d表示Dynamic
# 这样就不会顶掉容器中原有目录下的文件
-v /data/docker/registry:/tmp/registry

# 卷映射（命名卷），卷名前面不带/，卷映射不需要先在主机上创建目录/文件，比如对于配置文件不用提前复制一份到主机目录，但是配置文件一般不用卷映射
-v ngconf:/etc/nginx
```

- 运行带有容器卷存储功能的容器实例

```sh
# ro read only，默认容器权限为读写，只限制容器的权限，宿主机不限制
docker run -it --privileged=true -v 主机目录:容器内目录[:rw/ro] --name 自定义容器名 镜像名:标签
```

- 查看数据卷是否挂载成功，查看返回的Mounts信息可以知道是否挂载成功

```sh
docker inspect 容器id
```

```json
{
    "Mounts": [
        {
            // ...
            "Source": "/data/docker/registry",
            "Destination": "/tmp/registry",
            // ...
        }
    ],
}
```

- 继承其它容器的卷规则

```sh
docker run -it --privileged=true --volumes-from 要继承的容器的id --name 自定义容器名 镜像名:标签
```

## docker网络

- 网络模式

|网络模式|简介|命令|
|:-|:-|:-|
|bridge|虚拟网桥，默认模式，为每一个容器分配、设置ip等，并将容器连接到一个docker0|`--network bridge`，默认使用docker0|
|host|容器将不会虚拟出自己的网卡，配置自己的ip等，而是使用宿主机的ip和端口|`-–network host`|
|none|容器有独立的network namespace，但并没有对其进行任何网络设置，如分配veth pair和网桥连接，ip等|`–-network none`|
|container|新创建的容器不会创建自己的网卡和配置自己的ip，而是和一个指定的容器共享ip、端口范围等|`-–network container:name或容器id`|

- docker0网络，docker为每个容器分配唯一ip，容器之间可以使用`容器ip+容器端口`相互访问，但是这个ip在容器删了重新启动或者迁移的时候会改变，一般都会通过域名进行访问而不用ip

- docker0默认不支持主机域名，需要创建自定义网络，容器名就是主机域名

```sh
# 创建自定义网络
docker network create mynet

# 指定网络名
docker -run -d -p 5050:80 --name app1 --network mynet dpage/pgadmin4

# 进入容器内
docker exec -it app1 bash

# 容器内访问app2，直接用主机域名
curl http://app2:80
```

- 常用命令

```sh
# docker网络命令帮助
docker network --help

# 查看网络
docker network ls

# 查看网络源数据
docker network inspect 网络名

# 添加网络
docker network create 网络名

# 删除网络
docker network rm 网络名

# 查看docker容器内网络信息
docker inspect 容器id/容器名称 | tail -n 20
```

## docker权限

- 在 Docker 中，权限控制通常有三个层级
    - 默认模式：权限受限，最安全，但无法满足像 Vault 这类特殊应用的需求
    - cap_add：缺啥补啥，只给容器最必要的特权，是生产环境推荐的最佳实践
        - 比如设置为IPC_LOCK（全称是“进程间通信锁定”），它的核心作用是允许进程将内存锁定在物理内存（RAM）中，防止这部分内存被操作系统交换（Swap）到硬盘上
    - privileged: 如果设置为true，赋予容器几乎等同于宿主机 root 的全部权限。虽然能解决问题，但安全风险极高，通常不建议使用。

## Docker Compose

### 编写compose.yaml

```yaml
# 项目名
name: myproject
# services：定义一组要运行的容器服务
services: 
    # 服务名，名称自行定义
    mysql:
        # 容器名，不指定则为：项目名-服务名-数字-（1-n)
        container_name: mysql
        # 镜像名称
        image: mysql:8.0
        # 端口 
        ports:
            - "3306:3306"
        # 环境变量
        environment:
            # 必须
            MYSQL_ROOT_PASSWORD: mysql123
            # 下面的数据库名称、用户名和密码是可选的
            # 启动后，MySQL会自动创建dbname数据库和user_dbname用户并指定密码user_password，并授予该用户对dbname的全部权限
            MYSQL_DATABASE: dbname
            MYSQL_USER: user_dbname
            MYSQL_PASSWORD: user_password
        volumes:
            # 命名卷，不可以自定义位置，卷名要在顶级元素volumes里面声明
            - mysql-data:/var/lib/mysql
            # 绑定挂载，可以自定义位置，但是必须先在主机上创建目录 
            - /app/myconf:/etc/mysql/conf.d
        # 无论容器因为什么原因（容器内部程序崩溃、手动stop、Docker守护进程重启、系统重启）退出，都要自动重启
        restart: always
        # 网络，要在顶级元素networks里面声明，一个应用可以加入多个网络
        networks: 
            # 使用compose网络标识符
            - compose-net-name
        # 依赖：比如要先启动redis才能启动mysql
        depends_on:
            - myredis
    myredis:
# networks表示自定义网络
# 除非指定了external: true，否则无需先手动创建网络，compose能自动创建
networks: 
    # docker-compose文件内部使用的网络标识符，不是docker实际创建的网络名
    compose-net-name: 
        # docker实际创建的网络名称，不指定则为：项目名-compose网络标识符
        name: mynetname
        # 默认
        external: false
# volumes自定义卷
volumes: 
    # compose内部使用的卷名，不是docker实际创建的卷名
    mysql-data: 
        # docker实际创建的卷名，不指定则为：项目名-volume名
        name: myvolumename
configs: 
# 密钥
secrets: 
```

### 使用compose.yaml启动/下线

```sh
# 自动在当前目录查找compose.yaml、docker-compose.yaml
docker compose up -d [container1 container2 container3]

# 指定compose文件名批量新建容器并以后台方式启动
docker compose -f mycompose.yaml up -d

# 批量移除容器并删除相关资源（如果不在参数中指定不会删除自定义挂载的目录/卷）
docker compose -f mycompose.yaml down

# 批量启动
docker compose start container1 container2 container3

# 批量停止
docker compose stop container1 container2 container3

# container1启动三个实例
docker compose scale container1=3
```

## Dockerfile

Dockerfile时用来构建Docker镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

### Dockerfile执行流程

- docker从基础镜像运行一个容器

- 执行一条指令并对容器作出修改

- 执行类似`docker commit`的操作提交一个新的镜像层

- docker再基于刚提交的镜像运行一个新容器

- 执行Dockerfile中的下一条指令直到所有指令都执行完成

### Dockerfile基础

- `#`表示注释

- 每条保留字指令都必须为大写字母且后面要跟随至少一个参数

- 指令按从上到下的顺序执行

- 每条指令都会创建一个新的镜像层饼对镜像进行提交

### 保留字指令

- FROM

```Dockerfile
# 当前镜像是基于哪个镜像的，指定一个一级存在的镜像作为模板，第一条必须是FROM
FROM 基础镜像（镜像名:标签）
```

- LABEL

LABEL key=value key2=value2 ...

- RUN

容器构建(docker build)时需要运行的命令

```Dockerfile
# shell格式，<命令行命令>等同于在终端操作的shell命令
RUN 命令行命令

# exec格式
RUN ["可执行文件", "参数1", "参数2"]

# 等价于 RUN ./test.php dev offline
RUN ["./test.php", "dev", "offline"]
```

- EXPOSE

当前容器对外暴露出的端口

```Dockerfile
EXPOSE 端口号
```

- WORKDIR

指定在创建容器后，终端默认登录进来的工作目录

```Dockerfile
WORKDIR /
```

- USER

指定该镜像以什么样的用户执行，如果不指定，默认是root

- ENV

用来在构建镜像过程中设置环境变量，这个环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样；也可以在其它指令中直接使用这些环境变量

```Dockerfile
# 定义环境变量
ENV APP_HOME /usr/local/bin

# 使用环境变量
WORKDIR $APP_HOME
```

- VOLUME

容器数据卷，用于数据保存和持久化工作

- ADD

将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包，功能上相当于`COPY+解压`

- COPY

拷贝文件/目录到镜像中，将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置

```Dockerfile
# src为源文件或目录，dest为容器内的指定路径，该路径不用事先建好，不存在会自动创建
COPY src dest
COPY ["src", "dest"]
```

- CMD

指定容器启动（docker run）后要干的事情，Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换

```Dockerfile
# shell格式
CMD <命令>

# exec格式
CMD ["可执行文件", "参数1", "参数2"...]

# 参数列表格式，在指定了ENTRYPOINT指令后，用CMD指定具体的参数
CMD ["参数1", "参数2"...]
```

- ENTRYPOINT

也是用来指定容器启动（docker run）时要运行的命令，类似于CMD，但是不会被docker run后面的命令覆盖，而且这些命令行参数会被当做参数送给ENTRYPOINT指令指定的程序

```Dockerfile
ENTRYPOINT ["命令", "参数1", "参数2"...]

# 容器启动时执行.sh文件，并且不退出容器的写法
ENTRYPOINT ["bash", "-c", "/../*.sh && tail -f /dev/null"]
```

ENTRYPOINT可以和CMD一起使用，一般时变参才会使用CMD，这里的CMD等于是在给ENTRYPOINT传参。当指定了ENTRYPOINT后，CMD的含义旧发生了变化，不再是直接运行其命令而是将CMD的内容作为参数传递给ENTRYPOING指令，二者组合变成`<ENTRYPOINT> "<CMD>"`

假设Dockerfile：

```Dockerfile
ENTRYPOINT ["nginx", "-c"]
CMD ["/etc/nginx/nginx.conf"]
```

则docker命令和Dockerfile结合的实际命令如下表：

|docker命令|实际命令|
|:-|:-|
|docker run nginx:latest|nginx -c /etc/nginx/nginx.conf|
|docker run nginx:latest -c /etc/nginx/new.conf|nginx -c /etc/nginx/new.conf（docker run 后面带了参数变不会再根据Dockerfile改变）|

### 构建镜像

```sh
# 标签后有个空格，有个点
docker build -t 新镜像名:标签 .
```

### 构建带Java环境的Ubuntu镜像

```Dockerfile
FROM ubuntu:24.04
LABEL author=handle
# 设置环境变量，指定系统编码
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
# 登进容器后进入/usr/local
WORKDIR /usr/local
RUN mkdir /usr/local/jdk
# 把jdk添加到容器中，并且指定解压到容器中的具体位置
ADD bellsoft-jre21.0.4+9-linux-amd64.tar.gz /usr/local/jdk/
# 配置JAVA环境变量
ENV JAVA_HOME /usr/local/jdk/jre-21.0.4
ENV PATH $JAVA_HOME/bin:$PATH
# 容器启动后打开bash
CMD /bin/bash
```

### 构建微服务镜像

Dockerfile和jar包要在同一目录下

- 创建Dockerfile

```Dockerfile
FROM bellsoft/liberica-jre-ubuntu:21.0.4-9
# 作者信息
LABEL author=handle
# 在容器中创建目录
RUN mkdir -p /handle/book
# 把jar包添加到容器中的根目录下
COPY file-server-1.0.0-SNAPSHOT.jar /file-server.jar
# 容器启动时运行jar包
ENTRYPOINT ["java", "-jar", "-Dbook.absolutePath=/handle/book", "/file-server.jar"]
# 暴露端口
EXPOSE 2121
```

- 构建镜像

```sh
# 标签后有个空格，有个点（./），表示构建的整个上下文目录就是当前目录
docker build -f Dockerfile -t application:1.0 .
```

- 运行容器

```sh
# 后台运行
docker run -d -p 8080:8080 application:1.0
```

## 虚悬镜像

仓库名、标签都是`<none>`的镜像，虚悬镜像一级失去存在价值，可以删除

```sh
# 查看所有的虚悬镜像
docker image ls -f dangling=true

# 删除虚悬镜像
docker image prune
```
