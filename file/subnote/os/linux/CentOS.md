# CentOS

## 安装CentOS7

- 选Basic Web Server

手动设置三个分区

### `boot`分区

- 分区大小：`1g`
- 设备类型：`标准分区`
- 文件系统：`ext4`

### `swap`分区

- 分区大小：`内存多大就设置多大`
- 设备类型：`标准分区`
- 文件系统：`swap`

### `/`分区

- 分区大小：`总空间 - boot分区大小 - swap分区大小`
- 设备类型：`标准分区`
- 文件系统：`ext4`

- 生产环境还需设置开启kdump

## RPM包管理器

RPM：RedHat Package Manager，红帽软件包管理工具

RPM它生成具有.rpm扩展名的文件，类似Windows的setup.exe

RPM的理念是通用的，Linux的很多分发版都采用了它

```sh
# 安装rpm包
# -i：安装
# -v：verbose（提示）
# -h：hash（进度条）
rpm -ivh /path/to/file.rpm

# 查询已安装的rpm包
rpm -qa

# 查询软件包是否已安装
rpm -q 包名

# 查询软件包信息
rpm -qi 包名

# 查询软件包中的文件
rpm -ql 包名

# 查询文件所属的软件包
rpm -qf 文件名

# 卸载rpm包（erase）
rpm -e 包名
```

## yum包管理器

yum基于RPM包管理，能够从指定的服务器自动下载rpm包并安装，并且自动安装依赖包

```sh
# 搜索软件
yum search 包名

# 安装软件
yum install 包名

# 卸载软件
yum remove 包名

# 升级软件
yum update 包名

# 升级操作系统所有软件及内核，更新所有已安装包到最新版本，但会移除已废弃或替代的依赖包，可能影响系统稳定性
yum upgrade 

# 升级操作系统所有软件及内核，更新所有已安装包到最新版本，但保留旧的依赖包，避免潜在破坏系统
sudo yum update

# 查询可用jdk
yum search java|grep jdk

# 从查询结果中选择jdk安装
yum install java-1.8.0-openjdk-devel.x86_64
```

```sh
# 查看当前内核版本
uname -a

# 查看当前内核版本，显示可升级的内核版本
yum info kernel -q

# 升级内核，升级后重启在内核选择菜单选择新内核或旧内核
# 新内核兼容原来安装的软件
yum update kernel

# 查看已经安装的内核
yum list kernel -q
```
