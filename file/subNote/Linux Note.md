
# Linux Note

## Linux的目录结构

|序号|目录|描述|
|:-|:-|:-|
|1|/|根目录，在此目录下创建其它目录|
|2|/bin,usr/bin,usr/local/bin| binary的缩写，存放着最经常使用的命令|
|3|/sbin,usr/sbin,usr/local/sbin|存放着系统管理员使用的系统管理程序|
|4|/home|普通用户的主目录，每个用户都有一个自己的家目录，一般家目录名是用户的账号，如：/home/handle|
|5|/root|系统管理员的用户主目录|
|6|/lib|系统开机所需要最基本的动态链接共享库，类似Windows的dll文件，几乎所有的应用程序都需要用到这些共享库|
|7|/lost+found|这个目录一般情况下是空的，系统非法关机后，这里就存放了一些文件|
|8|/etc|所有的系统管理所需要的配置文件和目录，比如mysql的my.conf|
|9|/usr|用户的很多应用程序和文件都放在这个目录下，类似Windows的program files目录|
|10|/boot|存放启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件|
|11|/proc|不能动，这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息|
|12|/srv|不能动，service的缩写，该目录存放一些服务启动之后需要提取的数据|
|13|/sys|不能动，这是linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs|
|14|/tmp|用来存放一些临时文件|
|15|/dev|device的缩写，类似Windows的设备管理器，把所有的硬件用文件的形式存储|
|16|/media|linux系统会自动识别一些设备，例如U盘、光驱等，当识别后，Linux会把识别的设备挂载到这个目录下|
|17|/mnt|系统提供该目录是为了让用户临时挂载别的文件系统，我们可以将外部的存储挂载在/mnt上，然后进入该目录就可以查看里面的内容了|
|18|/opt|给主机额外安装软件存放的目录，如mysql、jdk等的安装文件|
|19|/usr/local|也是安装软件所安装的目录，一般是通过编译源码方式安装的程序，存放的是可执行程序|
|20|/var|存放着在不断扩充的东西，习惯将经常被修改的目录放在这里，包括各种日志文件|
|21|/selinux|security-enhanced linux，是一种安全子系统，它能控制程序只能访问特定文件，有三种工作模式，可以自行设置|

## Linux软件安装

### 安装`*.src.rpm`形式的源代码软件包

安装

```sh
# 将源代码编译并在/usr/src/dist/RPMS下生成二进制的rpm包
rpm -rebuild *.src.rpm
```

cd /usr/src/dist/RPMS
rpm -ivh*.rpm

卸载：rpm -e packgename
说明：rpm --rebuild*.src.rpm命令将源代码编译并在/usr/src/dist/RPMS下生成二进制的rpm包，然后再安装该二进制包即可。packgename如前所述，两种方法如下：

法1：
rpm -i your-package.src.rpm
cd /usr/src/redhat/SPECS
rpmbuild -bp your-package.specs           #一个和你的软件包同名的specs文件
cd /usr/src/redhat/BUILD/your-package/    #一个和你的软件包同名的目录
./configure                    #这一步和编译普通的源码软件一样，可以加上参数
make
make instal
法2：
rpm -i you-package.src.rpm cd /usr/src/redhat/SPECS  #前两步和方法一相同
rpmbuild -bb your-package.specs   #一个和你的软件包同名的specs文件
这时在/usr/src/redhat/RPM/i386/（根据具体包的不同，也可能是i686,noarch等等）在这个目录下，有一个新的rpm包，这个是编译好的二进制文件。
rpm -i new-package.rpm即可安装完成。

### 安装`*.tar.gz/*.tgz`或`*.bz2`形式的源代码软件包

- 解压文件

```sh
# 解压文件
tar -zxvf *.tar.gz
# 先解压然后进入解压后的目录
tar -jxvf *.tar.gz
```

- 配置安装路径

```sh
./configure --prefix=安装目标路径
```

- 编译

```sh
make
```

- 安装

```sh
make install
```

- 卸载

```sh
make uninstall
```

- 彻底删除安装路径

```sh
rm -rf 安装目标路径
```

## Linux常用命令

### Linux帮助命令

```sh
# 获得命令/配置文件的帮助信息
man 命令/配置文件

# 获得shell内置命令的帮助信息
help 命令

# 如果没有安装man和help，对于大多数命令
命令 --help
```

### 重启和关机

- 立即重启，`shutdown -r now` 或 `reboot` 或 `systemctl reboot`

- 立即关机，`shutdown -h now` 或 `halt` 或 `systemctl poweroff`

- 一分钟后关机，`shutdown` 或 `shutdown -h 1`

- 把内存数据同步到磁盘，`sync`

注意：虽然shutdown/reboot/halt等命令已经在关机前进行了`sync`，我们在重启或关机前最好还是执行一次`sync`

### 登录和注销

登录时尽量少用root账号登录，避免操作失误；当权限不够时，可以通过切换用户命令切换为管理员身份

- 切换用户

```sh
# 从权限高的用户切换到权限低的用户，不需要输入密码，反之需要输入密码
# 切换用户后返回到原来用户，使用 exit/logout
su - 用户名
```

- 注销，`logout`

注意：`logout`在运行级别3以下有效，在图形运行级别无效

### 用户管理

- 添加用户

```sh
# 创建用户成功后，会自动创建和用户名相同的家目录: /home/用户名
useradd 用户名

# 创建新用户
# 创建该用户的家目录，将该家目录的所有者设置为该用户
useradd -m 用户名

# 创建新用户并指定家目录
useradd -d 指定目录 用户名

# 创建新用户时指定用户组，如果不指定则系统默认创建一个和用户同名的组，并将用户添加到组中
useradd -g 用户组名称 用户名
```

- 删除用户

```sh
# 删除用户，但是保留用户的家目录
userdel 用户名

# 删除用户及用户的家目录，谨慎使用
userdel -r 用户名
```

- 指定/修改用户密码，不指定用户名时是对当前用户进行操作

```sh
passwd 用户名
```

- 查看用户信息

```sh
# 查看当前用户名（是登录用户，非切换后的用户）
whoami

# 查看当前用户信息（是登录用户，非切换后的用户）
who am i

# 查看指定用户的信息
id 用户名
```

#### 用户所在组

在Linux中，每个用户都必须属于一个组

```sh
# 修改用户所属的用户组
usermod -g 新用户组名称 用户名

# 改变该用户登录的初始目录，该用户要有进入该目录的权限才会成功
usermod -d 目录 用户名
```

#### 用户组

用户组类似于角色，系统通过用户组对有共性（权限）的用户进行统一管理

```sh
# 新增用户组
groupadd 用户组名称

# 删除用户组
groupdel 用户组名称

# 列出系统中已经存在的用户组
cat /etc/group
```

#### 用户和组相关文件

- /etc/passwd文件 用户的配置文件，记录用户的各种信息

每行的含义：用户名:口令(x):用户标识号(uid):组标识号(gid):注释性描述:主目录(家目录):登录Shell(一般是Bash shell)

- /etc/shadow文件 口令的配置文件

每行的含义：登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志

- /etc/group文件 用户组的配置文件，记录Linux包含的组的信息

每行的含义：组名:口令(x):组标识号(uid):组内用户列表

### 运行级别

运行级别说明：

- 0：关机

- 1：单用户【找回丢失密码】

- 2：多用户状态无网络服务

- 3：多用户状态有网络服务

- 4：系统未使用，保留给用户的运行级别

- 5：图形界面

- 6：系统重启

- 常用运行级别是3和5，可以指定默认运行级别

- 切换运行级别

```sh
init 运行级别
```

- CentOS 7 在/etc/inittab文件中进行了简化

multi-user.target:analogou to runlevel 3
graphical.target.target:analogou to runlevel 5

```sh
# 获取当前机器默认运行级别
systemctl get-default

# 设置当前机器默认运行级别
systemctl set-default multi-user.target
```

### 找回root密码

- 重启系统，在开机界面按“e”进入编辑界面

![开机界面](/images/开机界面.png)

- 在编辑界面光标往下移动，定位到“linux16”开头最在行的末尾，输入`init=/bin/sh`，完成后按“Ctrl + X”进入单用户模式

![编辑界面](/images/编辑界面.png)

- 在光标闪烁的位置输入`mount -o remount,rw /`，完成后按`回车`

- 在新的一行输入`passwd`，完成后按`回车`

- 然后输入新密码，完成后按`回车`；然后再次输入新密码，完成后按`回车`；显示passwd...的样式，说明密码修改成功

- 在光标闪烁的位置输入`touch /.autorelabel`，完成后按`回车`

- 在光标闪烁的位置输入`exec /sbin/init`，完成后按`回车`，等待系统自动修改密码，过程可能有点长，完成后系统会自动重启，就可以输入密码登录了

![单用户模式界面](/images/单用户模式界面.png)

### 文件/目录命令

#### 常用目录

```sh
# 当前目录
.

# 当前目录的上一级目录
..

# 家目录
~
```

#### 切换到指定目录

```sh
# 切换到指定目录
cd 目录

# 显示当前所在目录的绝对路径
pwd

# 切换到当前目录上一级目录
cd ..

# 切换到上上级目录
cd ../..

# 切换到家目录
cd ~
```

#### 增删改文件/目录

```sh
# 新建文件
touch 文件名

# 新建目录
# mkdir默认创建一级目录，通过指定选项 -p 创建多级目录
mkdir [-p] 目录名称

# 删除空目录
rmdir [可选项] 目录名称

# 删除文件/非空目录（谨慎操作）
# -r：递归
# -f：强制
rm [-rf] 文件/目录

# 复制文件/目录到指定目录
# \ 强制覆盖不提示 
# -r 递归整个目录
[\]cp [-r] 源文件/目录 目的目录

# 移动/重命名文件/目录
# 当源文件/目录与目的文件/目录名称一样时，就是重命名
# 当源文件/目录与目的文件/目录名称不一样时，就是移动并重命名
mv 源目录/文件 目的目录/文件
```

#### ls查看目录内容

```sh
# -a，展示当前目录下所有的文件和目录，包括隐藏的
# -h，human readable
# -l，以列表的方式显示信息
ls -ahl 文件/目录
```

#### cat查看文件内容

```sh
# cat只能浏览不能修改
# -n 显示行号
# 带上"| more"：将cat命令的处理结果交给more命令处理
cat [-n] 文件名 [| more]
```

#### more命令

more命令是基于vi编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容

```sh
more 文件名称
```

|快捷键|功能|
|:-|:-|
|空格|下一页|
|回车|下一行|
|ctrl + f|下一屏|
|ctrl + b|上一屏|
|=|当前行号|
|:f|文件名和当前行号|
|q|退出more|

#### less命令

less命令用来分屏查看文件内容，功能比more强大

less根据显示需要加载内容，对于大型文件具有更高的效率

```sh
less  文件名称
```

|快捷键|功能|
|:-|:-|
|空格|下一页|
|pagedown|下一页|
|pageup|上一页|
|/字符串|向下查找字符串，n向下查找；N向上查找|
|?字符串|向上查找字符串，n向下查找；N向上查找|
|q|退出less|

#### head命令

head用于显示文件的开头部分内容，默认前10行

```sh
# 查看文件前[任意行]内容
head [-n 任意正整数]  文件名称
```

#### tail命令

用于显示文件的末尾部分内容，默认末尾10行

```sh
# 查看末尾[任意行]内容
tail [-n 任意正整数]  文件名称

# 实时追踪该文档的所有更新
tail -f 文件名称
```

#### 文件/目录的所有者、所在组、其它组

- 在Linux中，每个文件都有所有者、所在组、其它组
    - 创建文件的用户就是该文件的所有者（当然创建文件后，所有者是可以改的）
    - 创建文件的用户所属组就是该文件的所在组
    - 对于该文件来说，该文件的所在组之外的组，就是其它组

```sh
# chown：change owner，修改文件/目录的所有者
# 可以先用ls命令查看文件的所有者信息
# 用户名必须是已经存在的
# -R：如果是目录，则使其所有子文件/子目录递归生效
chown [-R] 用户名 文件/目录

# 同时修改文件/目录的所有者和所在组
chown [-R] 用户名:用户组 文件/目录

# chgrp：change group，修改文件/目录的所在组
# -R：如果是目录，则使其所有子文件/子目录递归生效
chgrp [-R] 组名 文件/目录
```

#### 文件/目录权限

- `ls -l`显示内容的第一例有10位字符
    - 1.第0位确定文件类型
        - l：链接
        - d：目录
        - c：字符设备文件，如鼠标、键盘
        - b：块设备，如硬盘
        - `-`：普通文件
    - 2.第1-3位确定所有者拥有该文件的权限
    - 3.第4-6位确定所属组（的用户）拥有的该文件的权限
    - 4.第7-9位确定其它组（的用户）拥有的该文件的权限

- `ls -l`显示内容的第二列
    - 如果是一个文件，则为：1
    - 如果是一个目录，则为：子目录数+子文件数

- `ls -l`显示内容的第三列为文件/目录的所有者

- `ls -l`显示内容的第四列为文件/目录的所在组

- `ls -l`显示内容的第五列
    - 如果是文件，则为：文件大小（字节）
    - 如果是目录，则为：4096（字节）

- `ls -l`显示内容的第六列为：最后修改日期

- `ls -l`显示内容的第七列为：文件/目录名

##### rwx权限详解

- 对于文件
    - r：可读
    - w：可写，但是不代表可删除，可删除的前提是对该文件所在目录有写权限
    - x：可执行（对于可执行文件）

- 对于目录
    - r：可读，如ls查看目录内容
    - w：可写，如创建子目录，删除、重命名（子）目录
    - x：可进入该目录

##### 修改文件权限

- 第一种方式：
    - 通过`+`（增加权限）、`-`（撤回权限）、`=`（赋予权限）
    - `u`：所有者；`g`：所在组；`o`：其它组；`a`：所有人
- 第二种方式：通过数字变更权限
      - r=4,w=2,x=1,赋予了什么权限就将其权限值相加

```sh
# 相当于 chmod 751 文件/目录
chmod u=rwx,g=rx,o=x 文件/目录

chmod u-x,g+w 文件/目录
```

### echo命令

用于输出内容到控制台

```sh
echo [可选项] 输出内容

# 输出环境变量（要大写）
echo $PATH

# 输出字符串
echo "hello world"
```

### >命令

输出重定向（覆盖）

```sh
# 将输出到控制台的字符串重定向（覆盖写）到hello.txt（文件不存在会自动创建）
echo "hello world" > hello.txt
```

### >>命令

追加

```sh
# 将输出到控制台的字符串追加写到hello.txt
echo "hello world" >> hello.txt
```

### ln命令

软链接（符号链接），类似于windows里的快捷方式，存放了链接到其它文件的路径

```sh
# 给目录/文件创建一个软链接
ln -s 目录/文件 软链接名称

# 删除链接
rm 链接名称
```

### history命令

查看执行过的历史命令

```sh
# 显示最近执行过的任意条命令
history [任意正整数]

# 执行对应历史编号的命令
!历史命令编号
```

### 网络命令

#### 网卡配置

```sh
# 查看ip地址信息1
ip addr

# 查看ip地址信息2，此命令一般需要先安装相应工具
ifconfig

# 列出网络接口（设备）
# 网络接口（设备）被udev管理，通过systemd.link文件进行配置
# 前缀en表示有线/以太网，wl表示无线/WLAN，ww表示mobile broadband/WWAN
# lo： Virtual Loopback Interface（虚拟回环接口），是操作系统网络栈里的一种特殊接口，用来让主机和自己通信。它不是物理网卡，而是纯软件实现的网络接口。
# 只显示网络接口（设备）名称
ls /sys/class/net

# 除了显示网络接口（设备）名称外还有别的信息显示
ip link

# 进入对应网卡文件进行修改
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3

# 重启网络
service network restart
```

#### 防火墙命令

```sh
# 关闭防火墙1
service firewalld stop

# 关闭防火墙2
systemctl stop firewalld

# 禁止防火墙开机启动
systemctl disable firewalld

# 重启防火墙
firewall -cmd --reload

# 查看防火墙状态1
service firewalld status

# 查看防火墙状态2
systemctl status firewalld 

# 放行端口
firewall -cmd --zone=public --add -port=80/tcp --permanent
```

### 系统命令

```sh
# 显示操作系统的发行版号
uname -r

# 显示系统名、节点名称、操作系统的发行版号、内核版本等信息
uname -a

# 查看linux系统版本，适用于Redhat的Linux
cat /etc/redhat-release

# 总体内存占用，-m 用Mb单位来显示
free -m
```

### 日志命令

```sh
# 查看关于nginx的系统日志
cat /var/log/messages | grep nginx
```

### 压缩/解压命令

#### tar.gz文件

|选项|描述|
|:-|:-|
|-c|产生.tar打包文件|
|-v|显示详细信息|
|-f|指定压缩后的文件名|
|-z|打包同时压缩|
|-x|解包.tar文件|

```sh
# 压缩多个文件
tar -zcvf xxx.tar.gz 被压缩文件1 被压缩文件2

# 压缩文件夹
tar -zcvf xxx.tar.gz 被压缩文件夹

# 解压到当前目录
tar -zxvf xxx.tar.gz

# 解压到指定目录
tar -zxvf xxx.tar.gz -C path/to/directory
```

#### gz文件

```sh
# 将文件（不能压缩目录）压缩为*.gz文件
gzip 被压缩文件

# 解压
gunzip xxx.gz
```

#### zip文件

```sh
# 压缩文件
zip xxx.zip 被压缩文件

# 压缩目录
zip -r xxx.zip 被压缩目录

# 解压到当前目录
unzip xxx.zip

# 解压到指定目录
unzip -d path/to/directory xxx.zip
```

### 应用/端口命令

```sh
# 查看应用有没有启动
ps -ef|grep 应用名

# 查看应用占用的端口
netstat -tunlp|grep 应用名

# 查看端口有没有被某个进程占用
lsof -i:6379
```

### 日期/时间类命令

```sh
# 显示当前时间（年月日时分秒和星期）
date

# 显示当前年
date +%Y

# 显示为：年-月-日 时:分:秒
date "+%Y-%m-%d %H:%M:%S"

# 设置时间
date -s "2025-10-28 15:20:00"

# 显示当前日历信息（只显示本月日历）
cal

# 显示当前日历信息（显示2025年的日历）
cal 2025
```

### 查找命令

#### find

从指定目录向下递归遍历其各个子目录，将满足条件的文件或者目录显示在终端

|选项|功能|
|:-|:-|
|-name `文件名`|按照指定文件名查找文件|
|-user `用户名`|查找属于指定用户的所有文件|
|-size `文件大小`|按照指定大小查找文件|

```sh

find 指定目录 可选选项

# 前缀：+、-，表示大于、小于，不加前缀表示等于
# 单位：k、M、G
fine /home -size +10M
```

#### locate

快速定位文件路径

locate指令利用事先建立的系统中所有文件名称及路径的locate数据库实现快速定位

```sh
# 在第一次执行locate前，必须使用updatedb命令创建locate数据库
# 之后每次有新增、删除或者移动文件的操作，需要再次执行这个命令
updatedb

locate 文件名
```

#### which

查看指定命令在哪个目录下

```sh
which ls
```

#### grep命令和管道符号`|`

grep：过滤查找

`|`：将`|`左边命令的处理结果传递给右边的命令处理

|选项|功能|
|:-|:-|
|-n|显示匹配行及行号|
|-i|忽略字母大小写|

```sh
grep 可选选项 查找内容 源文件

# 如下两个命令的功能一样
grep "handle" /home/handle/hello.txt
cat /home/handle/hello.txt | grep "handle"
```

## vi/vim编辑器

vim就是vi的增强版

|序号|命令|描述|
|:-|:-|:-|
|1|`vi/vim 文件名`|打开文件|
|2|`i`|从一般模式进入编辑模式|
|3|`:`或`/`|从一般模式进入命令模式|
|4|`esc`|从编辑/命令模式进入一般模式|
|5|`:wq`|保存退出|
|6|`:q`|退出|
|7|`:q!`|强制退出，不保存|
|8|`[n]yy`|复制当前光标所在行[及其向下的共n行]|
|9|`p`|粘贴|
|10|`[n]dd`|删除当前光标所在行[及其向下的共n行]|
|11|`u`|撤销|
|12|`:set nu`|显示行号|
|13|`:set nonu`|隐藏行号|
|14|`n shift g`|跳转到第n行|
|15|`gg`|跳到文档开始行|
|16|`G`|跳到文档末尾行|
|17|`/关键字`|按回车开始查找，按`n`查找下一个|

## Red Hat系列Linux

### 安装CentOS7

- 选Basic Web Server

手动设置三个分区

#### `boot`分区

- 分区大小：`1g`
- 设备类型：`标准分区`
- 文件系统：`ext4`

#### `swap`分区

- 分区大小：`内存多大就设置多大`
- 设备类型：`标准分区`
- 文件系统：`swap`

#### `/`分区

- 分区大小：`总空间 - boot分区大小 - swap分区大小`
- 设备类型：`标准分区`
- 文件系统：`ext4`

- 生产环境还需设置开启kdump

### yum工具

```sh
# 搜索软件
yum search 软件名

# 安装软件
yum install 软件名

# 卸载软件
yum remove 软件名

# 升级软件
yum update 软件名

# 升级操作系统所有软件及内核，更新所有已安装包到最新版本，但会移除已废弃或替代的依赖包，可能影响系统稳定性
yum upgrade 

# 升级操作系统所有软件及内核，更新所有已安装包到最新版本，但保留旧的依赖包，避免潜在破坏系统
sudo yum update

# 查询可用jdk
yum search java|grep jdk

# 从查询结果中选择jdk安装
yum install java-1.8.0-openjdk-devel.x86_64
```

## Ubuntu

- 查看系统版本信息

```sh
lsb_release -a
```

## Arch Linux

官网：<https://archlinux.org/>

### pacman包管理器

```sh
# -S：同步
# y： 从服务器下载最新的软件包数据库（相当于更新索引）
# u： 升级所有已安装的软件包到最新版本
# 安装软件前先执行这一命令
sudo pacman -Syu

# 从远程仓库拉取软件安装
sudo pacman -S 软件名

# 安装本地.pkg.tar.zst安装包或.pacman安装包
sudo pacman -U path/to/pkg.tar.zst安装包或.pacman安装包

# 卸载软件
# R：删除指定包
# s：删除不需要的依赖（仅限于没有其他包需要的依赖）
# n：删除配置文件
sudo pacman -Rns package_name

# 搜索远程仓库的某个软件
pacman -Ss 软件关键字

# 搜索已安装的某个软件
pacman -Qs 软件关键字

# 查看已安装的所有软件
pacman -Q

# 列出包组的所有应用
sudo pacman -Sg kde-applications

# 选择安装包组里面的应用，默认全部安装，可以根据交互选择应用对应的数字，如果安装多个用空格隔开，来自定义安装
sudo pacman -S kde-applications

# 查看包组中还没安装的软件
sudo pacman -Sg plasma | grep -v " $(sudo pacman -Qg) "
```

### .AppImage软件包

下载.AppImage格式的软件包时，第一次先通过终端启动

如果运行异常，根据终端提示安装相应的依赖就可以了

```sh
# 比如有些AppImage软件包是需要fuse2的
sudo pacman -S fuse2
```

### .run软件包

.run软件包的卸载不好说，根据具体软件具体分析，下面的例子使用的是virtualbox

十分不推荐自己安装.run的软件包

```sh
# 给.run软件包执行权限
chmod +x path/to/软件包

# 执行安装，可能需要root权限
sudo path/to/软件包

# 卸载
sudo path/to/软件包 uninstall
```

### 从git源码编译安装

每一套源码一般要安装各种语言和依赖

可能语言和依赖下载下来都好几G了，但是编译出来的软件包可能才几M到几百M

这是针对没有官方编译版本的情况的无奈操作，平时自己别这么搞，完全费力不讨好

以yay为例，步骤如下

```sh
sudo pacman -Syu

# 先安装base-devel 和 git
sudo pacman -S --needed base-devel git

# 检查有没有安装go包（编译器）
go version

# 如果没有安装则安装
sudo pacman -S go

# 以下用指令非root用户执行
cd ~
git clone https://aur.archlinux.org/yay.git
cd yay

# 有可能因为网络原因导致安装失败，因此先修改PKGBUILD文件
vim PKGBUILD
# 找到 build() 函数，在多条 export 语句后追加保存退出
export GO111MODULE=on
export GOPROXY=https://goproxy.cn

# makepkg 使用当前目录下的 PKGBUILD 构建一个 Arch 包
# -s 自动安装构建所需的依赖（resolve dependencies）
# -i 构建完成后自动安装生成的 .pkg.tar.zst 包
makepkg -si

# 测试 yay 是否安装成功
yay --version
```

### 安装系统教程

可参考官方教程：<https://wiki.archlinux.org/title/Installation_guide>

- 下载Arch Linux系统镜像文件并校验：<https://archlinux.org/download/>

- 制作U盘启动
    - 如果是在windows系统制作，下载Rufus（Portable版本，免安装）：<https://rufus.ie>
    - 如果是在Linux系统制作，步骤如下：

```sh
# 列出系统中的磁盘分区表信息，找到U盘的设备名称，如：/dev/sdx
sudo fdisk -l

# 用dd命令把Arch Linux的ISO镜像写入到USB设备
# dd：一个底层复制工具，可以按块复制文件或设备
# bs=4M：设置块大小为4MB。这样每次读写4MB数据，比默认的512字节快很多
# if：输入文件（input file），填Arch Linux的ISO镜像
# of：输出文件（output file），填目标U盘的设备名称，如：/dev/sdx
# conv=fsync：在写完数据后调用 fsync()，确保数据真正写入磁盘，而不是停留在缓存里。避免写入未完成就拔掉设备
# oflag=direct：使用直接 I/O，绕过内核缓存，把数据直接写到设备。这样可以减少缓存污染，但速度可能略慢
# status=progress：在执行过程中显示进度信息（已写入字节数），否则 dd 默认是静默的
sudo dd bs=4M if=path/to/archlinux-version-x86_64.iso of=/dev/disk/by-id/usb-My_flash_drive conv=fsync oflag=direct status=progress

# 如果没有安装dd，可以直接用cp命令，但是不能设置每次的读写大小
sudo cp path/to/archlinux-version-x86_64.iso /dev/disk/by-id/usb-My_flash_drive
```

- 关闭BIOS的安全启动模式，不然可能会提示安全引导违规

#### 设置终端键盘布局和字体（可选）

```sh
# 列出可选的键盘布局
localectl list-keymaps

# 设置键盘布局为美式键盘（默认）
loadkeys us

# 设置当前终端的字体，立马见效，觉得当前终端字体不合适可以自行修改
# 分辨率为1080p的屏幕，建议选择 ter-122b
# 分辨率更高的屏幕，可以在 ter-124b，ter-128b，ter-132b 中选择一个, 默认lat9w-16
setfont ter-122b
```

#### 检查是否为UEFI启动

```sh
# Arch Linux只支持UEFI模式启动，输出为64就行了
cat /sys/firmware/efi/fw_platform_size
```

#### 连接网络，这里连接wifi为例

```sh
# 确认是否启用了网络接口
ip link

# 执行iwctl命令，进入wifi连接交互式命令行
iwctl

# 查看帮助
help

# 列出网卡设备，无限网卡一般为wlan0，后面以它为例
device list

# 让网卡扫描wifi设备
station wlan0 scan

# 列出可连接的wifi
station wlan0 get-networks

# 连接wifi，然后根据提示输入wifi密码
station wlan0 connect 上一步获取的某个wifi名称

# 退出wifi连接交互式命令iwctl
exit

# 测试网络是否连接成功
ping www.baidu.com 
```

#### 更新系统时钟

```sh
# 更新系统时钟是有必要的，因为下载软件是服务器会验证系统时间，如果时间不正确，可能出现下载失败的情况
# 将系统时间与网络时间进行同步
timedatectl set-ntp true 
# 查看系统时间状态，检查是否成功 看到（System clock synchronized :yes）这一句就是成功了
timedatectl status   
```

#### 磁盘分区

##### 1. 创建分区

只有EFI分区和`/`分区是必须的

如果内存有16或者32G（已经足够大了），可以不创建swap分区

```sh
# 列出设备和分区信息，记住要安装arc linux系统的设备，一般通过容量判断比较直观
fdisk -l

# 选择要分区的设备
fdisk /dev/the_disk_to_be_partitioned

# 首先输入g创建GPT分区表
g 

# 随后输入n创建新的分区
n

# 然后根据终端提示，输入分区编号，然后enter
# 然后根据终端提示，输入第一扇区，这一步保持默认，直接enter
# 然后根据终端提示，指定分区大小，+512M表示创建512M的分区
# 然后输入t改变分区类型，根据提示输入类型名称或者代码
t

# 依次创建efi分区（512M），类型为EFI System
# swap分区（和内存同样的大小），类型为linux swap
# /分区（设备容量剩余多少就设置多少），类型为linux filesystem

# 查看分区
p

# 保存并退出分区
w
```

##### 2. 格式化分区

```sh
# 格式化efi系统分区为fat32
mkfs.fat -F 32 /dev/efi_system_partition

# 格式化swap分区
mkswap /dev/swap_partition

# 格式化/分区为ext4
mkfs.ext4 /dev/root_partition
```

##### 3. 挂载分区

```sh
# 要先挂载根分区
mount /dev/root_partition /mnt

# 挂载efi系统分区，使用--mkdir创建/mnt/boot目录，并将efi分区挂载在/mnt/boot目录下
mount --mkdir /dev/efi_system_partition /mnt/boot

# 挂载swap分区
swapon /dev/swap_partition
```

#### 安装基础系统包

##### 配置pacman镜像源(可选)

非常建议配置，不然安装会很慢

```sh
# 用vim或nano编辑文件/etc/pacman.d/mirrorlist
vim /etc/pacman.d/mirrorlist
nano /etc/pacman.d/mirrorlist

# 在文件中加上如下国内镜像源，一般加一个就行了，加在文件中的Server行前面
Server = https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.sjtug.sjtu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

##### 用pacstrap安装基础系统包

pacstrap是Arch Linux安装环境提供的一个脚本，用来在目标挂载点（通常是新系统的根目录，比如 /mnt）安装基础系统包

pacstrap通常只用来安装基础系统包，安装完之后，可以用genfstab和arch-chroot进入新系统对其进行配置

```sh
# base，linux，linux-firmware分别是基础包组，linux内核和驱动程序
# Arch Linux 官方提供了 linux，linux-lts，linux-zen，linux-hardened内核，但是对于初学者，只推荐使用linux内核
pacstrap -K /mnt base linux linux-firmware
```

#### 配置系统

##### Fstab

为了在启动时挂载所需的文件系统（如用于引导目录/boot的文件系统），需要生成一个fstab文件

```sh
# -U：是以UUID的描述方式生成 fstab，>>：是将输出结果追加在后面的文件末尾
genfstab -U /mnt >> /mnt/etc/fstab
```

##### Chroot

为了在接下来的步骤中，直接跟新系统的环境、工具和配置交互，就像真的进入了新系统一样，需要通过arch-chroot切换到新系统

```sh
# 使用arch-chroot工具切换到新安装的系统，之后的操作就可以在新系统中完成了
# 比如用pacman安装的软件都将是安装到新系统中
# 执行完成终端用户目录的~会变成/
arch-chroot /mnt
```

##### 时间/时区设置

```sh
# 时区 /usr/share/zoneinfo/Region/City，可输入/usr/share/zoneinfo后按tab键查看可选时区
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 同步硬件时钟（当前系统时间写入硬件时钟，即主板上的独立时钟芯片）
# 系统启动时，内核会从硬件时钟读取初始时间
# 系统运行后，时间由内核维护（通常更精确，可以通过 NTP 校准）
# 执行hwclock --systohc后，系统会把当前内核时间写回硬件时钟，保证下次启动时不会“跑偏”
# 会创建并写入到/etc/adjtime文件中
hwclock --systohc
```

##### 本地化

```sh
# 需要先安装vim和terminus-font
# terminus-font是终端字体，后面设置FONT=ter-122b需要依赖这个字体
pacman -S vim terminus-font

# 编辑/etc/locale.gen
# 删除#en_US.UTF-8 UTF-8行和#zh_CN.UTF-8 UTF-8行的注释#并保存
vim /etc/locale.gen

# 生成locale
# 根据/etc/locale.gen的配置生成对应的locale数据文件
# 比如/usr/lib/locale/en_US.UTF-8和/usr/lib/locale/zh_CN.UTF-8
locale-gen

# 创建并编辑/etc/locale.conf
# 如果想显示英文就输入LANG=en_US.UTF-8并保存
# 如果想显示中文就输入LANG=zh_CN.UTF-8并保存
vim /etc/locale.conf

# 创建并编辑/etc/vconsole.conf
# 设置KEYMAP=us,FONT=ter-122b，这样新系统启动时终端字体（大小）就变成设置的了
vim /etc/vconsole.conf
```

##### 网络配置

```sh
# 设置主机名
vim /etc/hostname

# 安装网络管理器（不安装则需要手动配置网络）
pacman -S networkmanager

# 设置网络管理器开机启动
systemctl enable NetworkManager.service
```

##### Initramfs（了解）

- 前面用pacstrap安装基础系统包的时候安装了linux包，这一步不用再配置了

- Initramfs（Initial RAM Filesystem）是一个在系统启动时加载到内存里的临时根文件系统。

- 它包含了一些必要的工具和驱动，用来帮助内核完成启动过程，直到真正的根文件系统（比如你磁盘上的 / 分区）可以挂载。

- 启动流程中的角色
    - 1.BIOS/UEFI → 加载引导程序（GRUB）。
    - 2.GRUB → 加载 Linux 内核和 Initramfs 到内存。
    - 3.内核启动 → 挂载 Initramfs 作为临时根文件系统。
    - 4.Initramfs 脚本运行 → 加载驱动、解密磁盘、挂载真正的根文件系统。
    - 5.切换根目录 → 从 Initramfs 切换到真正的 /，继续启动用户空间程序（systemd 等）。

- 在Arch Linux里，initramfs是由mkinitcpio生成的

```sh
# 根据配置文件里的所有preset来生成initramfs
mkinitcpio -P
```

##### 设置root用户的密码

```sh
# 设置root用户的密码，根据提示输入密码
passwd
```

##### 安装微码更新（可选）

它是内核在启动时加载的一段数据，用来修复CPU的硬件bug和安全漏洞

```sh
# 查看cpu型号
cat /proc/cpuinfo | grep "model name"

# 如果是intel的cpu，安装 intel-ucode
pacman -S intel-ucode

# 如果是amd的cpu，安装 amd-ucode
pacman -S amd-ucode

# 如果卸载了微码更新，需要更新GRUB配置
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

##### 安装引导程序（GRUB）

GRUB：<https://wiki.archlinux.org/title/GRUB>

ESP = EFI System Partition（EFI 系统分区）

NVRAM（非易失性随机访问存储器）：是一种断电后仍能保持数据的非易失性RAM，用于存储系统设置和配置数据，包括BIOS设置、启动选项和硬件信息。

这些信息在系统开机时会自动加载到RAM中，可以被操作系统读取和修改

```sh
# 安装grub
# GRUB是引导加载程序
pacman -S grub

# 安装efibootmgr
# GRUB安装脚本使用efibootmgr将引导项写入NVRAM
pacman -S efibootmgr

# 安装grub到计算机
# --efi-directory：挂载的EFI系统分区（用实际的路径表示，而不是用挂载的路径）
# 前面根分区挂载到了/mnt，EFI系统分区挂载到了/mnt/boot，因而这里填/boot就行了

# --bootloader-id：引导加载器的标识符，这里命名为GRUB
# 将会在esp/EFI/中创建该标识符的目录，用来存储EFI二进制文件，并且该标识符将会出现在UEFI启动菜单中，以标识GRUB启动项

# 前面检查是否为UEFI模式启动时，输出为64（表示64位模式的UEFI，不要混淆噢不是指64位的CPU指令集，如果是32位请看官网教程），因此安装grubx64.efi
# 安装GRUB EFI应用程序grubx64.efi到esp/EFI/GRUB/，并且将其模块安装到/boot/grub/x86_64-efi/
# grub-install命令尝试在固件引导管理器创建一个引导选项（这里命名为GRUB了）
# 如果引导选项满了或者系统阻止操作引导顺序，将会失败
# 比如Thinkpad的BIOSs有一个设置项为"Boot Order Lock"，需要禁用它才能让efibootmgr进行添加或删除引导选项的操作
# 可以用efibootmgr删除不需要的引导选项
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB

# 打开文件
sudo vim /etc/default/grub

# 找到并设置如下两个属性，就可以默认快速开机不用等待了，需要的时候按ESC弹出菜单选项
GRUB_TIMEOUT=0
GRUB_TIMEOUT_STYLE=hidden

# 生成grub主配置文件/boot/grub/grub.cfg
# 默认情况下，生成脚本自动将所有已安装的Arch Linux内核的菜单项，添加到生成的grub主配置文件中
# 只要改动了/etc/default/grub或/etc/grub.d/，或者添加/删除了内核，都需要再次执行此命令重新生成grub主配置文件
grub-mkconfig -o /boot/grub/grub.cfg
```

##### 重启

```sh
# 退出chroot环境，执行完成终端用户目录的/会变成~
exit

# 取消挂载/mnt
umount -R /mnt

# 重启
reboot
```

#### 连接网络

```sh
# 如果安装了NetworkManager，先连接网络，这里以wifi为例
# 查看可用 Wi-Fi 网络
nmcli device wifi list

# 连接到指定 Wi-Fi 网络
nmcli device wifi connect "上面列出的Wifi的SSID" password "wifi连接密码"

# 查看连接状态
nmcli connection show --active

# 设置开机自动连接
nmcli connection modify "上面列出的Wifi的SSID" connection.autoconnect yes
```

#### 安装桌面环境（KDE）

KDE：<https://wiki.archlinux.org/title/KDE>

KDE由桌面环境Plasma、KDE Frameworks、KDE Applications三部分组成

但是安装时只用安装Plasma和KDE Applications，会自动安装依赖库KDE Frameworks

##### 安装plasma和kde-applications

motrix下载器无法接管火狐浏览器下载，可能是因为桌面放大的原因，设置好分辨率，保持原来的比例不要设置缩放

所以plasma应该完整安装，而kde-applications则是根据需要安装就行了

- 安装plasma和kde-applications的全部应用

```sh
# 最好不要这么做，其实可以安装完整的plasma，而kde-applications可以指定安装某一些就行了
sudo pacman -S plasma kde-applications
```

- 只安装plasma的部分应用

```sh
# plasma-desktop，桌面壳，包含面板、菜单、任务栏等基本界面，它是最小的plasma安装
# plasma-workspace，会话管理器、启动器、设置中心，安装plasma-desktop会将其作为依赖自动安装
sudo pacman -S plasma-desktop

# kde-gtk-config，官方的描述是同步KDE设置到GTK应用，人话就是让GTK应用（如Firefox）在KDE（基于QT）下的外观和行为更接近QT应用
# 必须安装kde-gtk-config，否则火狐浏览器的最大化和最小化按钮是不显示的
sudo pacman -S kde-gtk-config

# plasma-nm，管理网络连接，并且在任务栏显示网络托盘图标，可作为NetworkManager的GUI
# plasma-pa，音量控制器，并且在任务栏显示托盘小喇叭
# plasma-systemmonitor，系统监视器，类似Windows的任务管理器
# kscreen，显示设置，如设置分辨率、缩放等，不安装的话系统设置里面Display & Monitor选项置灰
sudo pacman -S plasma-nm plasma-pa plasma-systemmonitor kscreen
```

- 只安装kde-applications的部分应用

```sh
# konsole，终端模拟器，支持标签、透明、快捷键
# dolphin，文件管理器，支持标签、网络挂载、批处理等
sudo pacman -S konsole dolphin 
```

##### 安装SDDM（图形登录管理器）

完整的plasma只包含了sddm-kcm（sddm的kde桌面配置工具），还需要自行安装SDDM

不用单独安装xorg，安装sddm的时候会将其作为依赖自动安装

```sh
sudo pacman -S sddm

# 可选安装，可以在系统设置选择登录界面的样式
sudo pacman -S sddm-kcm

# 启用SDDM开机启动
sudo systemctl enable sddm
```

##### 创建非root用户

sddm登录界面只默认显示一个非root用户给你输入密码，然后登录进入桌面，因此这里要先创建一个非root用户

```sh
# 创建非root用户和家目录，这里以handle为例
useradd -m handle

# 指定handle的登录密码
passwd handle
```

##### （root用户）安装sudo

sudo在整个系统的使用过程中，使用频率是非常高的，不装只能通过`su - root`切换超级用户进行操作，非常麻烦

```sh
# 安装 sudo
pacman -S sudo
```

- 配置 sudo 权限，让wheel组的用户（普通用户）都能用sudo

```sh
# 如果直接用 vim /etc/sudoers，一旦写错，可能导致系统无法使用 sudo，必须进 root 修复
# 因此这里临时指定使用 vim 编辑器来打开并编辑 sudoers 文件
# visudo 会在保存前检查语法，防止你把 sudo 配坏
# 找到这行： # %wheel ALL=(ALL:ALL) ALL然后取消注释，保存退出
EDITOR=vim visudo

# 用户加入 wheel 组，然后重启，新的组权限才会生效，这里以上面创建的handle为例
# usermod 修改用户账户的命令
# -a append，追加组（不移除已有组）
# -G wheel 指定要加入的组是 wheel
usermod -aG wheel handle
```

##### 安装声音驱动

ALSA(Advanced Linux Sound Architecture)驱动作为linux内核的一部分，已经作为依赖安装了

台式机无需再安装高级的声音驱动

笔记本电脑如果没有声音，参考官网进行安装：<https://wiki.archlinux.org/title/General_recommendations#Sound_system>

##### 安装显卡驱动

- 先启用 multilib 仓库，它是Arch官方提供的32位兼容库仓库，Steam、Wine、某些游戏需要它,Steam的软件包也在这个仓库里面

```sh
# 编辑/etc/pacman.conf配置文件
sudo vim /etc/pacman.conf

# 找到这两行，取消注释，保存退出
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

- 根据显卡类型安装显卡驱动

```sh
# 这一步可能还要摸索，笔者装了英特尔和英伟达的后，重启黑屏了，然后切换tty又装了optimus-manager，然后重启又正常了

# 安装 GPU 驱动（根据显卡选择，虚拟机可以不用执行）

# 根据执行结果去网站找到对应的显卡代码：https://nouveau.freedesktop.org/CodeNames.html
# 到安装教程官网，根据显卡代码安装对应的显卡驱动：https://wiki.archlinux.org/title/NVIDIA
lspci -k -d ::03xx

# NVIDIA（闭源）
# nvidia：NVIDIA内核模块
# nvidia-utils：NVIDIA驱动工具，安装nvidia时会将nvidia-utils作为依赖进行安装
# lib32-nvidia-utils：NVIDIA驱动工具（32位）（可选）,Steam需要用到，笔者建议安装具体的软件的时候再根据交互提示选择安装
# nvidia-settings：NVIDIA图形驱动程序配置工具（可选）
sudo pacman -S nvidia lib32-nvidia-utils nvidia-settings

# 对于使用Wayland的情况，如Plasma(Wayland)，还需要进行DRM (Direct Rendering Manager) 内核模式设置
# 从nvidia-utils 560.35.03-5起,默认已经启用DRM
# 对于老版本的驱动，设置modeset=1
# 先确认，输出应该为Y
# 但是笔者安装玩驱动后执行此命令提示没有这个文件或目录，重启也正常显示
cat /sys/module/nvidia_drm/parameters/modeset

# AMD（开源）
# 官网教程：https://wiki.archlinux.org/title/Xorg#AMD
sudo pacman -S xf86-video-amdgpu mesa

# Intel（开源）
# 官网教程：https://wiki.archlinux.org/title/Intel_graphics
# mesa OpenGL 支持
# libva-intel-driver 视频加速（VA-API）
# vulkan-intel Vulkan 支持（如游戏、图形加速）
sudo pacman -S mesa libva-intel-driver vulkan-intel
```

##### 安装yay

AUR（Arch User Repository）是Arch Linux社区维护的一个用户贡献的包脚本仓库

AUR本身只提供PKGBUILD脚本，不提供二进制包

用户需要用makepkg手动下载源码、编译、打包，再用pacman安装

AUR助手（比如 yay、paru、pamac）就是帮你自动化这一整套流程的工具

yay (Yet Another Yogurt)是AUR助手之一，Yogurt：是一个早期的AUR助手

官网：<https://github.com/Jguer/yay>

- 1.下载官方编译版本.tar.gz,解压

- 2.`.bash_profile`文件中设置环境变量

```sh
export YAY_HOME=/home/handle/Applications/yay_x86_64
export PATH=$PATH:${YAY_HOME}
```

- 3.测试yay是否安装成功

```sh
yay --version
```

- 4.安装base-devel

```sh
# base-devel是使用yay -S 包名 构建AUR包时用到的依赖
# 它包含了常见的编译工具和脚本，如make、gcc等
sudo pacman -S --needed base-devel
```

##### 安装切换显卡工具

对于笔记本，安装完对应的显卡驱动后，还要安装切换显卡工具

笔者建议就是直接切换用独显或核显

```sh
# 安装完重启
yay -S optimus-manager

# 命令行切换显卡，nvidia, integrated, hybrid，分别为独显、集显和混合模式
# 切换到 Nvidia GPU
optimus-manager --switch nvidia
```

##### 安装防火墙

```sh
# 防火墙工具
sudo pacman -S firewalld

# 防火墙控制面板
sudo pacman -S plasma-firewall

# 启动防火墙
sudo systemctl start firewalld

# 设置防火墙开机自启动
sudo systemctl enable firewalld
```

##### 安装中文字体

Monospace：等宽，一个汉字=两个ASCII字母的宽度，全宽字符：宽度等于一个汉字的宽度，半宽字符：宽度等于半个汉字的宽度

Serif：有衬线，笔画末端有小装饰线

Sans-serif：无衬线，笔画末端没有装饰，线条简洁

![衬线和无衬线对比图](/images/Sans-Serif.png)

- 安装谷歌版的思源黑体（等比例/等宽）

```sh
# noto-fonts-cjk：谷歌版的思源黑体，覆盖简体、繁体、日文、韩文（cjk分别是中日韩的英文首字母）
# 不安装中文会乱码，装一个够了，不够用再找别的字体
sudo pacman -S noto-fonts-cjk

# 包含更多的汉字特别是生僻字和繁体字（据说是unicode包含的全部汉字），根据需要安装
# 笔者建议安装，因为fcitx5的候选字就有很多是生僻字的，不安装的话会显示为相应的unicode码
sudo pacman -S ttf-hanazono

# 然后用来刷新和重建系统的字体缓存（只能刷新部分字体缓存，如fcitx5的候选字就不会马上生效，需要重启才行）
# fc-cache：Fontconfig
# -f (force)：强制刷新，即使缓存已经存在也会重新生成
# -v (verbose)：详细模式，会在终端输出扫描过程和结果，方便确认哪些字体被识别
fc-cache -fv
```

- 系统字体设置

字体大小跟系统默认一样，除了Fixed width设置为"Noto Sans Mono CJK SC"，其它都用"Noto Sans CJK SC"，

特别是General那里，一定要设置，不然有些中字会显示半宽，特别别扭

![系统字体设置](/images/system-fonts.png)

##### 安装火狐浏览器

```sh
sudo pacman -S firefox

# 启动火狐浏览器
# 进入浏览器设置，搜索font，将字体设置为"Noto Sans CJK SC"（可选）
firefox
```

##### 安装Watt Tookit

证书配置向导页面：<https://steampp.net/liunxSetupCer>

```sh
# 去官网的github地址下载.tgz版本的软件包
# 解压软件包（同tar.gz)
# 打开软件，找到Settings-General，找到AppData data并跳转到该目录
# 找到该目录的子目录下的文件Plugins/Accelerator/SteamTools.Certificate.cer，一般该位置在~/.local/share/Steam++/Plugins/Accelerator/SteamTools.Certificate.cer
# 将这个cer文件复制到一个另一个目录，不然文件选择弹窗可能找不到上面的默认的位置

# 1.将证书导入到系统
sudo trust anchor --store 上面复制的SteamTools.Certificate.cer的具体路径

# 2.将证书导入到火狐浏览器
# 打开火狐浏览器，进入设置 - 隐私与安全 - 安全 - 证书 - 查看证书 ，选择 证书颁发机构( Authorities )，然后导入上面位置的证书
# 只勾选 信任由此证书颁发机构来标识网站，然后确定导入


# 3.将证书导入到Steam，这个暂时用不到，先不做笔记了
# steam依赖chrome内核的证书，笔者用的是火狐，懒得装其它浏览器了，直接放弃设置
# 最后导入完成后这个复制的cer文件可以删除掉
```

##### 安装输入法fcitx5

```sh
# fcitx5-im是一个元包，包含了fcitx5 fcitx5-gtk fcitx5-qt fcitx5-configtool
# fcitx5，主程序
# fcitx5-gtk fcitx5-qt，UI开发工具包的输入法模块，如果装有vscode，则必须安装，否则输入法会抽风
# fcitx5-configtool，GUI配置程序
# fcitx5-rime,一种可自定义的输入法引擎，但默认情况下其默认配置为拼音。
sudo pacman -S fcitx5-im fcitx5-rime

# 配置环境变量（官方建议的，其实一开始笔者没配置也没啥问题发生），在~/.bash_profile中添加如下内容，SDDM+KDE+Wayland的桌面环境只用添加这一行就可以了
# v2rayN和微信输入法需要设置GTK_IM_MODULE、QT_IM_MODULE和SDL_IM_MODULE，虽然官方和电脑提示不需要设置，不要听
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export SDL_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx

# 然后配置输入法
fcitx5-configtool

# 然后在input method添加：Rime
# 然后在input method，下方的configure addons，选择Classic User Interface,然后配置字体大小，然后应用直接见效，不然候选字真的太小了
# 然后在系统设置KeyBoard，找到virtual keyboard，选择Fcitx 5，然后应用就可以了
```

- 字体设置（可选）

都设置为"Noto Sans CJK SC"，字号、是否加粗用默认

![fcitx5字体设置](/images/fcitx5-fonts.png)

##### 安装输入法ibus-rime,(在vscode下会抽风，所以建议换成fcitx5)

```sh
# 安装
pacman -S ibus-rime

# 然后配置输入法
ibus-setup

# 然后在input method添加：Chinese-Rime
# 然后在系统设置KeyBoard，找到virtual keyboard，选择IBus Wayland，然后应用就可以了
```

##### KWallet

- kwallet-pam

```sh
# kwallet-pam：让KWallet在登录时自动解锁( 只针对Blowfish模式，gpg不可以)
# 可以管理vscode里面的的账号密码，vscode启动的时候就会提示了
# 安装完成后打开系统设置启动钱包服务，设置Blowfish密码和开机密码一样
# gpg模式还没有官方的登录解锁工具，开机每次都要输入一次密码，麻烦就先不用了
sudo pacman -S kwallet-pam

# 然后查看文件
cat /etc/pam.d/sddm

# 看看有没有登录解锁，一般kde+sddm都自动配置了
# 这样下次登录时，Blowfish 钱包会随登录密码自动解锁，不再弹窗
-auth       optional    pam_kwallet5.so
-session    optional    pam_kwallet5.so         auto_start
```

- kwalletmanager

```sh
# kwalletmanager：管理工具，如果不安装则在系统设置没有kwallet的管理项，根据需要决定是否安装
sudo pacman -S kwalletmanager
```

### 安装其它常用软件

```sh
# 压缩/解压软件ark
sudo pacman -S ark

# ark不支持rar解压，还需要安装unrar才能用ark解压rar文件
# 安装即可，无需配置ark
sudo pacman -S unrar

# 图片查看器 
sudo pacman -S gwenview

# 草莓音乐播放器
sudo pacman -S strawberry

# 视频播放器
sudo pacman -S haruna

# 录屏软件
sudo pacman -S obs-studio
```

#### 安装steam

```sh
# 需要启用multilib仓库
sudo pacman -S steam

# 安装完后首次用命令启动，不然可能弹不出界面，都不知道什么问题
steam
```

#### 安装wine

- 1.安装

```sh
# 需要启用multilib仓库来兼容32位的软件运行
sudo pacman -S wine

# 安装Wine的.NET替代运行环境，用于运行依赖.NET 的Windows程序（推荐）
# 如果不安装，首次启动wine时也会提示安装，但是这种安装下载速度可能很慢，容易失败，且不受系统包管理器管理（不推荐）
sudo pacman -S wine-mono
```

- 2.配置wine字体
    - 2.1复制Windows系统的字体到`/home/具体用户名/.wine/drive_c/windows/Fonts`
        - 2.1.1 直接复制Windows系统`C:/Windows/Fonts`文件夹
        - 2.1.2 wine安装7z解压工具，然后用7z打开从微软官方下载的ISO系统镜像文件，复制`\..\Windows.iso\sources\install.wim\1\Windows\Fonts`文件夹
            - install.wim里面有1-n个数字文件夹，选其中一个就行了
    - 2.2~~配置wine字体映射，这样Wine请求Windows字体时，就会自动用Arch系统的字体替代~~（此方法已废，有个软件更新后还是乱码了，手动映射不过来的）

```sh
# 打开wine注册表
wine regedit

# 进入HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes
# 添加字符串值（String Value）
# 笔者前面只安装了noto-fonts-cjk字体，所以都映射为"Noto Sans CJK SC","SC"是简体中文的意思
# "Noto Sans CJK SC"的得来请看后面的笔记
# 笔者目前只设置了"SimSun"="Noto Sans CJK SC"就没有乱码了，后面两个先列出来备用吧，哪天乱码了再编辑注册表补上
"SimSun"="Noto Sans CJK SC"
"MS YaHei"="Noto Sans CJK SC"
"KaiTi"="Noto Sans CJK SC"

# 如果想确认Wine替换后会用到哪个字体，如：
# fc-match SimSun，输出NotoSerif-Regular.ttf: "Noto Serif" "Regular"
# 则字符串值可以设置为"SimSun"="Noto Serif"
# 但是，"Noto Sans"是西文字体，中文覆盖不完整，中文可能显示方框或fallback到其它字体，还是推荐用"Noto Sans CJK SC"
# 所以总结得到：fc-match命令没卵用
fc-match SimSun

# 当然也可以设置为系统其它的字体的家族名
# 列出系统所有字体的家族名
# 如输出的其中一行为：Noto Serif CJK KR,Noto Serif CJK KR ExtraLight
# 表示这个字体文件既可以被识别为 “Noto Serif CJK KR”，也可以被识别为 “Noto Serif CJK KR ExtraLight”
# 在Wine注册表替换时，你通常只需要用主家族名（第一个名字）
# 第二个名字（ExtraLight）是具体的字重/样式别名，一般不用于注册表替换
fc-list : family
```

- 3.使用wine运行/卸载windows应用

```sh
# 安装/运行exe
wine path/to/exe文件

# 卸载windows程序
wine uninstaller

# wine配置
winecfg
```

#### 安装VirtualBox

十分不推荐自己安装.run的软件包

```sh
# 然后根据提示选择virtualbox-host-modules-arch
# 这个包是专门为Arch官方的默认内核linux编译好的VirtualBox内核模块
# 安装它后无需自己编译，也不需要额外安装linux-headers
sudo pacman -S virtualbox

# 如果还需要虚拟机增强功能（共享剪贴板、共享文件夹等），可以安装
sudo pacman -S virtualbox-guest-utils
```

#### 安装debtap

对于pacman仓库没有的软件，可以找yay有没有

如果yay也没有，那就去官网看看有没有

如果官网只有.deb格式的包，那么就只能用debtap来转成.pkg.tar.zst包再进行安装了

```sh
# 安装
yay -S debtap

# 更新
sudo debtap -u

# 进行转换，根据提示进行交互
sudo debtap path/to/file.deb

# 安装转换后的包
sudo pacman -U path/to/file.pkg.tar.zst
```

### 挂载其它硬盘

#### 使用partitionmanager进行分区和挂载

如果改硬盘已经在/etc/fstab中了要先删掉，不然会重复生成

```sh
# 安装分区工具
sudo pacman -Syu partitionmanager

# 如果是挂载ntfs格式的硬盘，还要安装ntfs-3g，否则只能读
sudo pacman -S ntfs-3g

# 如果想要完整支持exFAT，比如U盘（exFAT支持超过4GB的单文件，适合U盘跨平台使用），还要安装exfatprogs
sudo pacman -S exfatprogs

# 如果想要完整支持fat32，还要安装dosfstools
sudo pacman -S dosfstools
```

#### 使用命令行

```sh
# 先创建一个目录作为挂载点
sudo mkdir /mnt/data

# 将硬盘挂载到该目录
sudo mount /dev/sdb1 /mnt/data

# 生成该挂载点的fstab条目，追加到/etc/fstab文件中
sudo genfstab -U /mnt/data >> /etc/fstab

# 确认是否正确挂载了
cat /etc/fstab
```

#### 修改挂载点的权限

```sh
# 修改目录的所有者和所在组
sudo chown -R 当前用户:当前用户 /mnt/data

# 修改目录的读写权限
sudo chmod u=rwx,g=rx,o=rx /mnt/data
```

### 创建菜单/桌面快捷方式

- 如果只想创建桌面快捷方式，则在`~/Desktop`目录下创建`应用名.desktop`文件

- 如果想要在菜单也显示该应用，则在`~/.local/share/applications`目录下创建`应用名.desktop`文件，然后在菜单找到对应的应用名称，鼠标右键-添加到桌面

- 以微信为例，内容如下:

```content
[Desktop Entry]
Type=Application
Name=WeChat
Exec=/home/handle/Applications/WeChat.AppImage
Icon=/home/handle/Applications/WeChat.png
Terminal=false
```

- 如果是wine，则Exec键值对需要做变动

```sh
Exec=wine /path/to/App.exe
```

### 获取下载文件的哈希码

```sh
sha256sum path/to/下载文件
```

### 系统启动美化

试了一下就重启的时候比较明显，开机的时候根本看不到，没什么卵用的，不建议安装

- 安装plymouth和GUI

```sh
# plymouth
sudo pacman -S plymouth

# plymouth主题选择的GUI
sudo pacman -S plymouth-kcm
```

- 编辑grub配置模板并生成配置文件

```sh
# GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet splash"
sudo vim /etc/default/grub

sudo grub-mkconfig -o /boot/grub/grub.cfg
```

- 修改 initramfs HOOKS

```sh
# HOOKS=(... plymouth ...)
# 如果用的是systemd，则plymouth要放在其后面
# 如果系统用dm-crypt加密，则plymouth要放在encrypt or sd-encrypt之前
sudo vim /etc/mkinitcpio.conf

# 重新生成 initramfs
mkinitcpio -P
```

### 其它可能用到的命令

- 加载/卸载内核模块

```sh
# kmod工具包用于管理内核模块，它作为内核依赖已经安装
# 手动加载内核模块
modprobe module_name

# 也可以使用以下任一命令，通过指定文件名加载没有安装到/usr/lib/modules/kernel_release/里面的模块
insmod file_name module_options
modprobe file_name

# 使用以下任一命令卸载/删除一个模块
rmmod module_name
modprobe -r module_name
modprobe --remove module_name
```

- 确认网卡驱动是否已加载

```sh
# pci网卡
lspci -k 

# usb网卡，但是笔者试了没有打印相关的驱动信息，这个命令废了
# 还不如直接用lsusb，输出更简洁
sudo lsusb -v

# usb网卡，可以看到类似：usbcore: registered new interface driver rtl8187
sudo dmesg | grep usbcore
```

- 确认网络接口设备是否已创建

```sh
# 列出可用的网络接口设备
ip link

# 启用网络接口设备，以网络接口设备名称：wlp2s0，为例
ip link set wlp2s0 up
```

- 创建分区表/分区

```sh
# 列出设备和分区信息
fdisk -l

# 选择要分区的设备
fdisk /dev/the_disk_to_be_partitioned

# 首先输入g创建GPT分区表
g 

# 随后输入n创建新的分区
n

# 然后根据终端提示，输入分区编号，然后enter
# 然后根据终端提示，输入第一扇区，这一步保持默认，直接enter
# 然后根据终端提示，指定分区大小，+512M表示创建512M的分区
# 然后输入t改变分区类型，根据提示输入类型名称或者代码
t

# 查看分区
p

# 保存并退出分区
w
```

- 创建（格式化）/挂载/卸载文件系统（分区）

```sh
# 列出设备和分区信息
fdisk -l

# 创建一个新的文件系统（即格式化为指定的文件系统），前提是该分区必须已经卸载
# 不同文件系统对应不同的命令，以分区/dev/sda1为例
mkfs.ext4 /dev/sda1
mkfs.exfat /dev/sda1
mkfs.fat -F 32 /dev/sda1

# 挂载一个文件系统，以分区/dev/sda1，挂载点/mnt/data为例
mount /dev/sda1 /mnt/data

# 卸载一个文件系统，以分区/dev/sda1，挂载点/mnt/data为例
umount /dev/sda1

# 也可以是
umount /mnt/data

# 列出所有已挂载的文件系统，可以加上分区名进行筛选，不然内容太多了
findmnt [/dev/sda1]

# 列出系统已经存在的存储设备（文件系统），包括已挂载和未挂载的
lsblk -f
```

- 查看硬盘是否4k对齐

```sh
# 只列出设备/分区名，对齐情况两列
# 如果是0→已对齐
lsblk -o NAME,ALIGNMENT

# 或者手动计算分区的起始扇区是否能被8整除，如果是，说明4k对齐
sudo fdisk -l
```
