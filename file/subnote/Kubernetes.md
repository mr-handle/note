# Kubernetes

Kubernetes是谷歌开源的容器编排引擎，用来管理容器化的应用程序和服务，如部署、扩展、管理等

Kubernetes提供了容器编排的功能，可以通过配置文件来定义应用程序的部署方式

让容器的创建、维护和管理变得更加简单和高效

Kubernetes也提供了很多高可用的特性，比如自动重启、自动重建、自我修复等

还有可扩展性，让系统可以根据负载的变化来动态地扩展或缩减系统的资源，从而提高系统的性能和资源的利用率

还有其它如灾难恢复、弹性伸缩等，这些特性都可以帮助提高应用程序的性能、可用性和稳定性

## Kubernetes组件

### node（节点）

一个节点就是一个物理机或虚拟机，在一个节点上，可以运行一个或多个pod

### pod

pod是Kubernetes的最小调度单元

一个pod就是一个或多个应用容器的组合

pod创建了一个容器的运行环境，在这个环境中，容器间可以共享一些资源

比如网络、存储以及一些运行时配置等

一般情况下，一个pod中只运行一个容器

### srvice（服务）

将一组pod封装成一个服务，这个服务可以通过一个统一的入口来访问

- service的类型有：
    - ClusterIP：默认类型，集群内部的服务
    - NodePort：节点端口类型，将服务公开到集群节点上，然后就可以通过节点的ip地址和端口访问服务
    - LoadBalancer：负载均衡类型，将服务公开到外部负载均衡器上
    - ExternalName: 外部名称类型，通过返回一个CNAME记录，将服务映射到一个外部域名上
    - Headless：无头类型，可以创建一个没有ClusterIP的服务，主要用于DNS解析和服务发现

### ingress

用来管理从集群外部访问集群内部服务的入口和方式

可以通过ingress配置不同的转发规则

从而根据不同的规则，来访问集群内部不同的service以及service所对应的后端pod

还可以通过ingress来配置域名，这样就可以将原本使用ip和端口的方式转换成使用域名的方式来访问service了

另外ingress还可以配置其它的功能，如负载均衡、SSL证书等

### ConfigMap

将一些配置信息封装起来，然后就可以在应用程序中读取和使用了

有了ConfigMap，就可以将配置信息和应用程序的镜像内容分离开来

这样就可以保持容器化应用程序的可移植性

### secret

ConfigMap的配置信息都是明文的，如果配置信息包含一些敏感信息如账号密码，就不建议将其存储在ConfigMap中，secret就是为了解决这个问题的

secret可以将一些敏感信息封装起来，然后就可以在应用程序中读取和使用了

但是secret也只是做了一层Base64的编码而已，还需要配合Kubernetes的其它手段来提高安全性

如user、c.role、sa

### volume

将持久化存储的资源挂载到集群中的本地磁盘上或者集群外部的远程存储上

### Deplyment

定义和管理应用程序的副本数量，以及应用程序的更新策略

可以简化应用程序的部署和更新操作

### StatefulSet

和Deplyment类似，也提供了定义和管理应用程序副本数量、动态扩容缩容等功能

还保证了每个副本都有自己稳定的网络标识符和持久化存储

因此，像数据库、缓存、消息队列等这些有状态的应用，以及一些保留了会话状态的应用程序，一般都使用StatefulSet

对于数据库，更好的方案是从Kubernetes分离出来，单独部署

## Kubernetes架构

Kubernetes是典型的master-worker架构

master节点负责管理整个集群，worker节点负责运行应用程序和服务

worker节点为了能够对外提供服务，每个node都会包含3个组件：kubelet、kube-proxy和container-runtime（容器运行时环境）

- kubelet
    - 负责管理和维护每个节点上的pod，并确保它们按照预期运行
    - 也会定期从api-server组件接收新的或者修改后的pod规范，
    - 同时它也会监控工作节点的运行情况，然后将这些信息汇报给api-server

- kube-proxy
    - 负责为pod对象提供网络代理和负载均衡服务
    - 它会在每个node上启动一个网络代理，使发往service的流量以一种高效的方式路由到正确的pod中

- container-runtime可以是Docker-Engine、Containerd、CRI-O、Mirantis Container Runtime

master节点包含4个组件：kube-apiserver、etcd、ControllerManager和scheduler

- kube-apiserver
    - 负责提供kubernetes集群的API接口服务，所有的组件都会通过这个接口来进行通信
    - 还负责对所有资源对象的增删改查等操作进行认证、授权和访问控制
- scheduler
    - 负责监控集群中所有节点的资源使用情况，然后根据调度策略，将pod调度到合适的节点上运行
- ControllerManager
    - 负责管理集群中各种资源对象的状态，如pod、node、service等，根据它们的状态做出相应的响应，确保集群中的各种资源对象都处于预期的状态
- etcd
    - 一个高可用的键-值存储系统，存储集群中所有资源对象的状态信息

## Helm

kubernetes的包管理工具，帮助我们更方便地管理kubernetes的应用，有点类似于yum

使用它可以方便地安装、升级和管理应用

## 下载kubernetes

minikube是一个轻量级的kubernetes发行版，可在本地计算机上创建虚拟机，并部署仅包含一个节点的简单集群

此外k3s、k3d、kind也可以用来搭建本地的轻量级kubernetes环境

交互工具：kubectl是一个命令行工具，Dashboard是一个webui界面，此外还有API接口，它们都可以用来跟创建的kubernetes集群进行交互，管理集群中的各种资源

### 使用minikube

下载minikube：<https://github.com/kubernetes/minikube>

```sh
# 解压得到可执行文件，将其改名为minikube
tar -zxvf minikube-linux-amd64.tar.gz 

# 将当前用户加入docker用户组并立即生效
sudo usermod -aG docker $USER && newgrp docker

# 查看minikube版本
./minikube version

# 进入目录下启动minikube
# --driver=docker：指定驱动为docker
# --image-mirror-country=cn：指定使用国内镜像
./minikube start --driver=docker --image-mirror-country=cn

# 查看minikube状态
./minikube status

# 停止minikube
./minikube stop

# 因为没有独立安装kubectl，可以使用minikube内置的，但是命令比较长
# 查看集群中的节点
./minikube kubectl get nodes
```

### 使用k3s

k3s官网：<https://k3s.io/>

k3s是一个CNCF认证的轻量级kubernetes发行版，更加轻量快速，而且集成了kubernetes的大部分架构和功能，相比较minikube这样的单节点kubernetes集群，k3s可以方便地搭建一个多节点集群

下载稳定版：<https://update.k3s.io/v1-release/channels/stable>

选择文件名是k3s的下载，它是一个可执行文件，别的带image的是容器镜像文件，不要下载它们

```sh
# 下载完后赋予可执行权限
sudo chmod +x k3s

# 启动，会占用当前终端
sudo ./k3s server

# 查看节点
sudo ./k3s kubectl get nodes

# 查看pod
sudo ./k3s kubectl get pods -A
```

### 配置k3s镜像源

```sh
# 新建文件并添加如下内容，然后重新启动k3s
sudo vim /etc/rancher/k3s/registries.yaml

mirrors:
    docker.io:
        endpoint:
            - "https://45hrqeao.mirror.aliyuncs.com"
            - "https://hub-mirror.c.163.com"
```

#### 创建和配置worker节点

```sh
# 从master节点获取一个token，作为创建worker节点的一个认证凭证
sudo cat /var/lib/rancher/k3s/server/node-token

# 在worker节点上用master的地址和token启动来加入集群
sudo ./k3s agent --server https://master_ip:6443 --token master_token

# 然后在master上可以看到worker节点了
sudo ./k3s kubectl get nodes
```

## kubectl

需要在master节点上执行

```sh
# 查看资源对象状态
# all：列出集群中所有的资源对象
kubectl get nodes/pod/svc/其它资源对象/all

# 查看pod所在节点和pod的ip
kubectl get pod -o wide

# 访问pod所提供的服务
# 一般最好不要直接使用pod来提供服务
# 首先，pod使用的是一个集群内部的ip，这个ip是不能直接从集群外部访问到的
# 其次，pod并不是一个非常稳定的实体，经常会被创建或销毁，这时候它的ip也可能发生变化
# 作为解决方案，可以使用service来对外提供服务
curl pod的ip

# 创建pod，会下载nginx镜像，就算用的是docker驱动，docker已经有nginx镜像了还是会下载，它会用自己的镜像
kubectl run nginx --image=nginx

# 可以看到创建的nginx的创建状态
kubectl get pod

# 查看create命令帮助
kubectl create -h

# 创建deployment，会根据配置创建pod
# 在deployment和pod之间还有一个中间层replicaSet，用来管理pod副本数量，也被创建了
# deployment管理replicaSet，replicaSet管理pod
# 我们只管创建deployment，让k8s自己管理replicaSet和pod
kubectl create deployment nginx-deployment --image=nginx

# 可以看到创建的nginx-deployment的创建状态
kubectl get deployment

# 查看replicaset状态
kubectl get replicaset

# 查看pod状态
# 列出的pod名字构成：kubectl create deployment 指定的名字-replicaset随机串-pod随机串
kubectl get pod

# 编辑deployment，这种方式不常用，但是很快
kubectl edit deployment deploymentName

# 查看日志
kubectl log podName

# 进入容器
# --：告诉kubectl，后面的内容不再是kubectl的参数，而是要传给容器的命令
# 退出容器用exit就可以了
kubectl exec -it podName -- /bin/bash

# 删除资源对象
kubectl delete deployment deploymentName
```

## 创建service

```sh
# 创建/删除服务
kubectl create/delete service serviceName

# 将一个已经存在的deployment对外公开为一个服务
kubectl expose deployment deploymentName

# 查看服务的详细信息
# 如果想要查看其它资源类型的详细信息，只要把service改成其它资源类型就可以了
kubectl describe service serviceName
```

## kubernetes命名空间

命名空间用来对集群中的资源进行隔离和分组的一种机制

这样在不同的命名空间中的资源就不会因为名字相同而产生冲突了

一般可以用来隔离不同的项目或者不同的环境

如果不指定命名空间，创建的资源对象就会放到default这个命名空间中

平时输入的各种命令，如果不指定命名空间，默认也都是在default命名空间中执行的

```sh
# 查看命名空间
kubectl get namespace
```

## kubernetes配置文件(yaml)

```yaml
# 指定使用哪个版本的api，用来定义怎样和apiserver交互，格式：group/version
# group有apps（应用）、batch（批处理）、autoscaling（自动扩缩容）等
apiVersion: apps/v1
# 指定资源对象类型：有Deployment、Service、ConfigMap等
# 不同的资源对象的配置会有所不同，需要根据具体情况来写配置文件，而不是用一个固定的模板
kind: Deployment
# 定义资源对象的元数据，比如资源对象的名称、标签、命名空间等
metadata:
    # 如定义创建的deployment名称
    name: containerName-deployment
# spectification的缩写，定义各种资源对象的配置信息,里面还可以嵌套spec
# 如这里是定义deployment的配置信息
spec:
    # 用来选择特定资源
    selector:
        matchLabels:
            app: nginx
    # 副本数
    replicas: 2
    template:
        metadata:
            labels:
                app: nginx
        # 定义pod的配置信息
        spec:
            containers:
                -   name: nginx
                    # 使用的镜像和版本
                    image: nginx:1.25
                    ports:
                        # 对外暴露的端口
                        - conntainerPort:  80
```

```sh
# 通过配置文件创建/删除资源对象
kubectl create/delete -f /path/to/config.yaml

# 应用配置文件中的内容到集群中（可能是创建或更新资源对象）
kubectl apply -f /path/to/config.yaml
```

### service配置文件

```yaml
# 指定使用哪个版本的api，用来定义怎样和apiserver交互，格式：group/version
# group有apps（应用）、batch（批处理）、autoscaling（自动扩缩容）等
apiVersion: v1
# 指定资源对象类型：有Deployment、Service、ConfigMap等
# 不同的资源对象的配置会有所不同，需要根据具体情况来写配置文件，而不是用一个固定的模板
kind: Service
# 定义资源对象的元数据，比如资源对象的名称、标签、命名空间等
metadata:
    # 如定义创建的Service名称
    name: serviceName
# spectification的缩写，定义各种资源对象的配置信息,里面还可以嵌套spec
# 如这里是定义deployment的配置信息
spec:
    # 服务类型，默认是ClusterIP类型的服务，它只能在集群内部访问到
    # 指定为NodePort，节点端口类型的服务
    type: NodePort
    # 用来选择特定资源
    selector:
        # 指定所有app是nginx的资源
        app: nginx
    ports:
        -   protocol: TCP
            # 对外暴露的端口
            port:  80
            # serviec背后的pod对应的端口
            targetPort: 80
            # 节点对外提供服务的端口，在外部通过浏览器访问集群服务的就用这个端口访问，要求必须在30000~32767之间
            nodePort: 30080
```

## Portainer

kubernetes的可视化管理工具

下载配置文件：<https://downloads.portainer.io/ce-lts/portainer.yaml>

然后去掉文件里面portainer/templates/rbac.yaml和portainer/templates/pvc.yaml这两个部分的内容，以及和volume有关的内容，不然pod会一直pending启动不了

如果是k3s的话至少要有一个master节点和一个worker节点

笔者现成的配置文件：[poratiner.yaml](/file/kubernetes/portainer.yaml)

```sh
# -n：指定命名空间
kubectl apply -n portainer -f /path/to/portainer.yaml

# 查看portainer状态
kubectl get all -n portainer

# 重启portainer（pod）
kubectl rollout restart deployment portainer -n portainer
```

- 访问：<http://masterIP:30777>，第一次访问会要求设置一个密码
