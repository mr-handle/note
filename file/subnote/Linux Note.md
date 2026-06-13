
# Linux Note

software RAID devices：把多块硬盘组合成一个更安全或更快的虚拟设备，例如把/dev/sda和/dev/sdb做成/dev/md0

- LVM volumes：
    - 第一层：物理硬盘（Physical Disk）
    - 第二层：物理分区，如/dev/sda1、/dev/sda2
    - 第三层：物理卷 PV（Physical Volume），物理分区可以变成物理卷
    - 第四层：卷组 VG（Volume Group）
    - 第五层：逻辑卷 LV（Logical Volume）：是一个虚拟分区，传统分区如：/dev/sda1、/dev/sda2不能扩扩缩容，但是虚拟分区可以

```sh
# 创建物理卷
pvcreate

# 创建卷组
vgcreate

# 创建逻辑卷
lvcreate
```

## inode

- 硬盘以扇区（sector）为最小物理存储单位，而操作系统和文件系统以块（block）为单位进行读写，块由多个扇区组成
- 固态硬盘虽然没有物理扇区，但使用逻辑块，与传统硬盘的块类似
- 文件的数据存储在块中
- 文件的元信息（inode号、文件类型、权限、所有者、大小、修改时间等）存储在inode中，每个文件都有唯一的inode
- inode不存储文件的数据，而是存储指向文件数据的指针，操作系统通过指针找到并读取文件数据
- inode是一种固定大小的数据结构，在文件系统创建时就确定了
- 系统可以通过inode号直接定位到文件的元信息，无需遍历整个文件系统
- inode的数量是有限的，每个文件系统只能包含固定数量的inode

```sh
# 查看文件的inode信息
stat /path/to/filename
```

## 用户态和内核态

- 用户态(User Mode) :
    - 用户态运行的进程可以直接读取用户程序的数据，拥有较低的权限。
    - 当应用程序需要执行某些需要特殊权限的操作，例如读写磁盘、网络通信等，就需要向操作系统发起系统调用请求，进入内核态。

- 内核态(Kernel Mode) ：
    - 内核态运行的进程几乎可以访问计算机的任何资源包括系统的内存空间、设备、驱动程序等，不受限制，拥有非常高的权限。
    - 当操作系统接收到进程的系统调用请求时，就会从用户态切换到内核态，执行相应的系统调用，并将结果返回给进程，最后再从内核态切换回用户态。

内核态相比用户态拥有更高的特权级别，因此能够执行更底层、更敏感的操作。不过，由于进入内核态需要付出较高的开销（需要进行一系列的上下文切换和权限检查），应该尽量减少进入内核态的次数，以提高系统的性能和稳定性。

- 用户态切换到内核态的 3 种方式：
    - 系统调用（Trap）：这是最主要的方式，是应用程序主动发起的。
        - 比如，当我们的程序需要读取一个文件或者发送网络数据时，它无法直接操作磁盘或网卡，就必须调用操作系统提供的接口（如 read(),send()）, 这会触发一次从用户态到内核态的切换。
    - 中断（Interrupt）：这是被动的，由外部硬件设备触发。
        - 比如，当硬盘完成了数据读取，会向 CPU 发送一个中断信号，CPU 会暂停当前用户态的程序，切换到内核态去处理这个中断。
    - 异常（Exception）：这也是被动的，由程序自身错误引起。
        - 比如，我们的代码执行了一个除以零的操作，或者访问了一个非法的内存地址（缺页异常），CPU 会捕获这个异常，并切换到内核态去处理它。

在系统的处理上，中断和异常类似，都是通过中断向量表来找到相应的处理程序进行处理。区别在于，中断来自处理器外部，不是由任何一条专门的指令造成，而异常是执行当前指令的结果。

最后，需要强调的是，这种状态切换是有性能开销的。因为它涉及到保存用户态的上下文（寄存器等）、切换到内核态执行、再恢复用户态的上下文。因此，在高性能编程中，我们常常需要考虑如何减少这种切换次数，比如通过缓冲 I/O 来批量读写文件，就是一个典型的例子。

## Linux的目录结构

Linux 使用一种称为目录树的层次结构来组织文件和目录

|序号|目录|描述|
|:-|:-|:-|
|1|/|根目录，在此目录下创建其它目录|
|2|/bin,usr/bin,usr/local/bin|binary的缩写，存放二进制可执行文件，最常用的命令一般都在这里（ls、mkdir、cp等）|
|3|/sbin,usr/sbin,usr/local/sbin|存放二进制可执行文件，系统管理员使用的系统级别的管理命令和程序|
|4|/home|普通用户的主目录，每个用户都有一个自己的家目录，一般家目录名是用户的账号，如：/home/handle|
|5|/root|系统管理员的用户主目录|
|6|/mnt|临时挂载点，系统提供该目录是为了让用户临时挂载别的文件系统，我们可以将外部的存储挂载在/mnt上，然后进入该目录就可以查看里面的内容了|
|7|/media|系统会自动识别一些设备，把自动识别出来的设备挂载到这个目录下（例如U盘、光驱等）|
|8|/etc|所有的系统管理所需要的配置文件和目录，比如mysql的my.conf|
|9|/usr|用户的很多应用程序和文件都放在这个目录下，类似Windows的program files目录|
|10|/usr/local|也是安装软件所安装的目录，一般是通过编译源码方式安装的程序，存放的是可执行程序|
|11|/opt|optional，系统之外的、可选安装的第三方软件放在这里，如vscode解压后得到的整个文件夹|
|12|/tmp|用于存放各种临时文件，是公用的临时文件存储点|
|13|/lib,lib64|存放着和系统运行相关的库文件，类似Windows的dll文件，几乎所有的应用程序都需要用到这些共享库|
|14|/boot|存放用于系统引导时使用的各种文件|
|15|/dev|device的缩写，类似Windows的设备管理器，把所有的硬件用文件的形式存储|
|16|/lost+found|这个目录一般情况下是空的，系统非法关机后，这里就存放了一些“无家可归”的文件|
|17|/var|存放着在不断扩充的东西，习惯将经常被修改的目录放在这里，包括各种日志文件|
|18|/proc|不能动，这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息|
|19|/selinux|security-enhanced linux，是一种安全子系统，它能控制程序只能访问特定文件，有三种工作模式，可以自行设置|
|20|/srv|不能动，service的缩写，该目录存放一些服务启动之后需要提取的数据|
|21|/sys|不能动，这是linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs|

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

## 命令

- 命令选项的参数写法

|选项类型|写法|
|:-|:-|
|短选项`-x`|如果是必选参数，可以写成`-x参数`或`-x 参数`，标准写法是后者；如果是可选参数，必须写成`-x参数`|
|短选项组合`-xyz`|`-xyz 参数`，这个参数只会绑定到组合里的最右边的选项，只适用`-z`是必选参数的情况，建议带参数还是不要用组合写法了|
|长选项`--x`|如果是必选参数，可以写成`--x 参数`或`--x=参数`；如果是可选参数，必须写成`--x=参数`|

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
# su即switch user
# 切换用户，但不加载该用户的登录环境，当前目录等不会切换
su 用户名

# 切换用户，并加载该用户的完整登录环境，当前目录变成切换后的用户家目录
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
# -m，创建该用户的家目录，将该家目录的所有者设置为该用户
# -G，加入wheel，但是也会创建和用户名相同的用户组
# -s，指定用户登录时使用的默认shell为/bin/bash，一定要确认该bash存在，不然会导致用户登录不了，这种情况执行："chsh -s path/to/正确的bash 用户名"来纠正
useradd -m [-G wheel] [-s /bin/bash] 用户名

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

# 查看所有用户
cat /etc/passwd
```

#### 用户所在组

在Linux中，每个用户都必须属于一个组

```sh
# 修改用户所属的用户组
usermod -g 新用户组名称 用户名

# 移除用户组的某个用户
gpasswd -d 用户名 用户组

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

# 修改用户组
sudo groupmod -n 新组名 旧组名

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

# 切换到上一个操作所在目录
cd -
```

#### 增删改文件/目录

```sh
# 新建文件
touch 文件名

# 新建目录
# mkdir默认创建一级目录，
# -p：parents，确保目录路径中的所有父级目录都被创建
# -m 755，设置文件权限，所有者拥有读、写、执行权限，所属组和其他用户只有读、执行权限
mkdir [-p] [-m 755] 目录名称

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

# 只展示文件（"^-"表示"-"开头）
ls -l 目录 | grep "^-"

# 统计目录下的文件数
ls -l 目录 | grep "^-" | wc -l

# 统计目录下的子目录数
ls -l 目录 | grep "^d" | wc -l

# 统计目录下的文件数，包括子目录里面的
ls -Rl 目录 | grep "^-" | wc -l

# 以树状展示目录结构
# 一般要手动安装tree
# arch安装命令：sudo pacman -S tree
tree 目录
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

![ll第一列内容示例](/images/ll第一列内容示例.png)

- `ls -l`显示内容的第一例有10位字符
    - 1.第0位确定文件类型
        - `-`：普通文件，如（图像、声音、视频、pdf、text、源代码等）
        - d：目录（directory）
        - l：链接（link）
        - c：字符设备文件，如鼠标、键盘
        - b：块设备文件（block），如硬盘
        - p：管道文件（pipe），用于进程之间的通信
        - s：套接字文件（socket），用于进程间的网络通信，也可以用于本机之间的非网络通信
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
    - r：可读，也可以用数字4表示
    - w：可写，但是不代表可删除，可删除的前提是对该文件所在目录有写权限，也可以用数字2表示
    - x：可执行（对于可执行文件），也可以用数字1表示

- 对于目录
    - r：可读（ls查看目录内容）
    - w：可写，如创建、删除文件/（子）目录，重命名（子）目录
    - x：可进入该目录（cd）
        - 如果对目录没有x权限，就算对该目录下的文件有读写权限也不能查看、修改
        - 如果对目录没有x权限，就算对目录有w权限，也不能删除该目录下的文件

##### 修改文件权限

- 第一种方式：
    - 通过`+`（增加权限）、`-`（撤回权限）、`=`（赋予权限）
    - `u`：所有者；`g`：所在组；`o`：其它组；`a`：所有人（all）
    - 如果不指定u/g/o/a，chmod默认使用a
- 第二种方式：通过数字变更权限
      - r=4,w=2,x=1,赋予了什么权限就将其权限值相加

```sh
# 相当于 chmod 751 文件/目录
chmod u=rwx,g=rx,o=x 文件/目录

chmod u-x,g+w+x,o+r 文件/目录

# 给文件所有者、所在组、其它组都加x权限的三种写法
chmod u+x,g+x,o+x 文件/目录
chmod a+x 文件/目录
chmod +x 文件/目录
```

##### 给文件加锁

```sh
# 加锁
chattr +i /etc/passwd

# 移动chattr到别的目录
mv /usr/bin/chattr /path/to/target

# 继续给chattr改名，这样任何人都无法执行chattr -i /etc/passwd解锁了
# 想要解锁的时候，将文件名改回chattr并移动到原来的目录，就可以了
mv /path/to/chattr /path/to/target

# 解锁
chattr -i /etc/passwd
```

- 除了给文件加锁外，还可以使用SUID，SGID，Sticky设置特殊权限

- 还可以用chkrootkit或rootkit hunter检测rootkit脚本（rootkit是入侵者使用工具）

- 还可以用Tripwire检测文件系统完整性

### tr字符处理命令

tr专门做字符级别的转换、删除、压缩

- 用法

```sh
tr [OPTION]... STRING1 [STRING2]
```

|选项|描述|
|:-|:-|
|-c或-C|取反|
|-d|删除|
|-s|压缩|
|-t|截断|

|字符集|描述|
|:-|:-|
|a-z|小写字母|
|A-Z|大写字母|
|0-9|数字|

- 例

```sh
# 把一组字符替换成另一组字符，输出：ABC123
echo "abc123" | tr 'a-z' 'A-Z'

# 把符合的子串abc截断成12一样的长度，得到ab，将ab替换为12，c不受影响，输出：12c123
echo "abc123" | tr -t 'a-z' '12'

# 删除字符，输出：abc
echo "abc123" | tr -d '0-9'

# 压缩重复字符，输出：abc123
echo "aabc123" | tr -s 'a'

# 取反，将所有非小写字母和换行符都变成d，输出：abcddd
# 写成echo "abc123" | tr -c 'a-z' 'd'，控制台会输出：abcdddd[用户名@主机名 ~]$，因为它把换行符也取反了
echo "abc123" | tr -c 'a-z\n' 'd'
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

- 硬链接（Hard Link）：
    - 和源文件的inode号相同，对文件系统来说是完全平等的，删除其中一个对另一个没有影响
    - 只有删除了源文件和它所有的硬链接文件，该文件才真正被删除
    - 不能对目录以及不存在的文件创建硬链接
    - 硬链接不能跨越文件系统

- 软链接/符号链接（Symbolic Link）：
    - 类似于Windows系统的快捷方式，存放了链接到其它文件的路径
    - 软链接和源文件的inode号不同
    - 源文件删除后，软链接依然存在，只是指向的是一个无效的文件路径
    - 可以对目录以及不存在的文件创建软链接
    - 软链接可以跨越文件系统

```sh
# 给目录/文件创建一个软链接
# -s：ln命令默认创建的是硬链接，通过指定-s创建软链接
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
|-f|指定压缩后的文件名/要解压的压缩包文件名|
|-z|tar只负责打包/解包，不负责压缩/解压，把数据推给其它压缩/解压程序处理|
|-x|解包.tar文件|

```sh
# 压缩多个文件
tar -zcvf xxx.tar.gz 被压缩文件1 被压缩文件2

# 压缩文件夹
tar -zcvf xxx.tar.gz 被压缩文件夹

# 解压到当前目录
tar -zxvf xxx.tar.gz

# 解压到指定目录
tar -zxvf xxx.tar.gz -C /path/to/directory
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
unzip -d /path/to/directory xxx.zip
```

### 文件传输命令

远程文件写法：user@remote:/home/handle/file.txt

```sh
# scp 即 secure copy，安全复制
# 用于通过SSH协议进行安全的文件传输，可以实现从本地到远程主机的上传和从远程主机到本地的下载
# 例如：scp -r my_directory user@remote:/home/user ，将本地目录my_directory上传到远程服务器 /home/user 目录下。scp -r user@remote:/home/user/my_directory ，将远程服务器的 /home/user 目录下的my_directory目录下载到本地。需要注意的是，scp 命令需要在本地和远程系统之间建立 SSH 连接进行文件传输，因此需要确保远程服务器已经配置了 SSH 服务，并且具有正确的权限和认证方式。
scp [-r] 源文件/目录 目的文件/目录

# 在本地和远程系统之间高效地进行文件复制，并且能够智能地处理增量复制，节省带宽和时间
rsync [-r] 源文件/目录 目的文件/目录 
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
find /home -size +10M

# 删除指定目录下10天前的所有tar.gz文件
find /path/to/directory -atime +10 -name "*.tar.gz" -exec rm -rf {} \;
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

# 忽略大小写搜索 /var/log/syslog 中所有包含 error 的行
grep -i "error" /var/log/syslog
```

### alias定义命令别名

```sh
# 查看所有别名
alias

# 创建别名
alias 别名='命令'

# 删除别名
unalias unalias 别名

# 上面的创建和删除别名都是在当前终端生效，只要关闭了终端就没了
# 想要让别名在终端永久生效，可以写入 ~/.bashrc，如果使用zsh的话可以添加到~/.zshrc中，但是笔者没试过
alias ll='ls -l'
```

### crond任务调度

任务调度：系统在某个时间执行的特定的命令或程序（周而复始地反复执行）

- 任务调度分类：
    - 1.系统工作：有些重要的工作必须周而复始地执行，如病毒扫描
    - 2.用户工作：个别用户可能希望执行某些程序，如备份数据库

- 用法：

```sh
crontab [options] file
crontab [options]
crontab -n [hostname]

# 重启任务调度
service crond restart

# 1.执行
crontab -e

# 2.在调度文件中输入任务，wq保存
*/10 * * * * date >> /tmp/out.txt
# 记得给当前用户赋予file.sh的执行权限
*/10 * * * * /path/to/file.sh
```

|序号|选项|描述|
|:-|:-|:-|
|1|-e|编辑定时crontab任务|
|2|-l|列出crontab任务|
|3|-r|删除当前用户所有的crontab任务|

- cron语法（注意每个时间之间有空格）：`* * * * *`，从左往右每个`*`含义如下

|项|含义|范围|
|:-|:-|:-|
|第1个`*`|一小时当中的第几分钟|0-59|
|第2个`*`|一天当中的第几小时|0-23|
|第3个`*`|一月当中的第几天|1-31|
|第4个`*`|一年当中的第几月|1-12|
|第5个`*`|一周当中的星期几|0-7（0和7都表示星期日）|

- 特殊符号说明

|序号|特殊符号|描述|
|:-|:-|:-|
|1|*|表示任何时间，如第一个*就表示一小时中每分钟都执行|
|2|,|表示不连续的时间，如`0 8,12,16 * * *`表示每天的8点整、12点整、16点整都执行一次|
|3|-|表示连续的时间，如`0 5 * * 1-6`表示周一到周六的5点整执行|
|4|*/n|表示每个多久执行一次，如`*/10 * * * *`表示每隔10分钟执行|

- 星期几和几号最好不要同时出现，它们定义的都是天，容易混乱

### at定时任务

- at命令是一次性的定时计划任务，at的守护进程atd以后台模式运行，检查任务队列运行定时任务
- 默认情况下，atd守护进程每60秒检查任务队列，有任务时，会检查任务运行时间，如果与当前时间匹配，则运行此任务
- 在使用at命令的时候，一定要保证atd进程的启动

```sh
# archlinux安装at
sudo pacman -S at

# 检查atd是否已经启动
ps -ef | grep atd

# at命令格式，输入完后按两次Ctrl+D结束具体任务内容输入
at 选项 时间

# 步骤1：任务时间
at 8am tomorrow

# 步骤2：任务内容
date > /tmp/date.log

# 查询在等待执行的定时任务
atq

# 删除任务，先通过atq得到任务号
atrm 任务编号
```

|选项|描述|
|:-|:-|
|-m|当指定的任务被完成后，将给用户发送邮件，即使没有标准输出|
|-I|查询，atq的别名|
|-d|删除，atrm的别名|
|-v|显示任务将被执行的时间|
|-c|打印任务的内容到标准输出|
|-V|显示版本信息|
|-q <队列>|使用指定的队列|
|-f <文件>|从指定文件读入任务而不是从标准输入读入|
|-t <时间参数>|以时间参数的形式提交要运行的任务|

|时间|描述|
|:-|:-|
|hh:mm|如果当天该时间已经过去，就会在第二天执行，如04:00|
|midnight或noon或teatime|使用比较模糊的词语来指定时间，teatime一般是下午4点|
|采用12小时计时制，时间后加上am或pm|如8am|
|指定具体日期，month day或mm/dd/yy或dd.mm.yy|指定的日期必须跟在指定的时间后面，如04:00 2026-01-03|
|now + 数字 时间单位|时间单位有minutes、hours、days、weeks，如now + 5 minutes|
|today或tomorrow|直接使用这两个词语中的某个指定时间|

### Linux分区

一个硬盘可以分很多个分区，但是对于Linux来说，它就只有一个根目录，一个独立且唯一的文件结构，每个分区都是用来组成这个文件系统的一部分

#### 硬盘说明

Linux硬盘分IDE硬盘和SCSI硬盘，此外还有nvme固态硬盘，目前基本上是SCSI硬盘

- 对于IDE硬盘，驱动器标识符为`hdx~`
    - `hd`：分区所在设备的类型，这里就是指IDE硬盘了
    - `x`：盘号，a为基本盘，b为基本从属盘，c为辅助主盘，d为辅助从属盘
    - `~`：分区，前4个分区用数字1-4表示，它们是主分区或扩展分区，从5开始就是逻辑分区
    - `hdb2`：第二个IDE硬盘上的第二个主分区或扩展分区
- 对于SCSI硬盘，驱动器标识符为`sdx~`
    - `sd`：分区所在设备的类型，这里就是指SCSI硬盘了
    - 其余和IDE硬盘的表示方法一样
- 对于nvme固态硬盘，待补充

```sh
# 查看所有设备挂载情况
# -f：列出文件系统信息
lsblk [-f]

# 查看系统整体磁盘使用情况
df -h

# 查看指定目录的磁盘占用情况
# -s：只显示汇总
# -h：人类可读格式
# -a：统计包含文件，而不只是目录
# -d：最大子目录深度，为"-d 0"时和-s结果一样
# -c：生成汇总
du -h 目录
```

## 环境变量

- 按照作用域来分
    - 用户级别环境变量 :`~/.bashrc`、`~/.bash_profile`
    - 系统级别环境变量 : `/etc/bashrc`、`/etc/profile`、`/etc/profile.d`、`/etc/environment`

- 上述配置文件执行先后顺序为：`/etc/environment` –> `/etc/profile` –> /`etc/profile.d` –> `~/.bash_profile` –> `/etc/bashrc` –> `~/.bashrc`

- `etc/profile.d`被`/etc/profile`加载

```sh
# 设置环境变量，让子进程（如新打开的终端、脚本）继承
# 实际上配置文件也是一个脚本文件
# 设置环境变量就是用export语句，将shell变量输出为环境变量
export 变量名=变量值

# 让修改后的配置信息立即生效
source /path/to/config_file

# 查看环境变量的值
echo "$PATH"

# 查看所有的环境变量
env
```

## 网络

### NAT网络配置

![NAT网络配置](/images/NAT网络配置.png)

主机上有一个虚拟网卡vmnet8，这个网卡可以跟虚拟机的网卡相互ping通，它们在同一个网段

虚拟机访问互联网要通过主机的虚拟网卡，无线网卡对虚拟网卡的请求进行代理

### 网络命令

```sh
# 查看ip地址信息1
ip addr

# 查看ip地址信息2
# 此命令一般需要先安装相应工具
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

# 修改主机名，编辑/etc/hostname文件并保存重启
vim /etc/hostname

# 设置主机名/域名和hosts映射，
# 编辑/etc/hosts文件，输入：10.0.2.5 你的主机名/域名
vim /etc/hosts

# 监听本机，将来自ip:22的连接数据保存到/var/log/tcpdump.log
# ens33表示本机网卡
tcpdump -i ens33 host ip and port 22 >> /var/log/tcpdump.log
```

### 主机名解析机制

- 例子：在浏览器输入www.baidu.com访问百度网页
    - 1.浏览器先检查浏览器缓存有没有该域名信息，如果有就使用缓存完成域名解析
    - 2.如果没有，就检查系统缓存有没有该域名信息，如果有就使用系统缓存完成域名解析
    - 3.如果没有，就检查系统hosts文件中有没有配置对于的域名IP映射，如果有就使用它完成域名解析
    - 4.如果没有，则到域名服务（DNS）解析域名得到IP
    - 5.如果DNS也没有，则返回域名不存在

### 防火墙命令

```sh
# 开启/关闭防火墙，开启/关闭防火墙自启动，查看防火墙状态
systemctl <start|stop|enable|disable|status> firewalld

# 查看所有开放的端口，观察ports关键字列出的端口，默认是空的
firewall-cmd --list-all

# 查看某个端口是否开放
firewall-cmd --query-port=端口/协议

# 打开/关闭端口，需要重新加载防火墙才会生效
firewall-cmd --permanent --zone=public --add-port=端口/协议
firewall-cmd --permanent --zone=public --remove-port=端口/协议

# 重新加载防火墙
firewall-cmd --reload
```

### 监控网络状态

#### netstat

netstat（net-tools包）已经过时，推荐使用ss命令（arch的iproute2包）

```sh
# 查看系统网络状况
# -an：按一定顺序排列输出
# -p：显示那个进程正在调用
netstat [选项]

# 可以查看端口使用的协议
netstat -anp

# 查看应用占用的端口
netstat -tunlp [| grep 应用名]

# 查看端口有没有被某个进程占用
lsof -i:6379

# 检测两个主机间的网络连接是否联通
ping [域名|IP]

# 统计连接到服务器的各个ip情况，并按连接数从大到小排序并截取前两条
# awk -F " " '{print $5}'：按空格分割，取第5段的字符串
# cut -d ":" -f 1：按:分割，取第1段
# uniq -c：统计个数，统计前要先排序
# sort -nr：从大到小排序
# head -2：截取前面两条
netstat -an | grep ESTABLISHED | awk -F " " '{print $5}' | cut -d ":" -f 1 | sort | uniq -c | sort -nr | head -2
```

## 进程

在Linux中，每个执行的程序都称为一个进程，系统为每个进程都分配一个进程号（pid）

- 每个进程都可能以两种方式存在：前台和后台
    - 前台进程：用户可以在屏幕上进行操作的进程
    - 后台进程：用户无法在屏幕上看到，但实际上在执行的进程
    - 一般的系统服务都是以后台进程的方式存在，而且开机后常驻系统中，直到关机才结束

### ps命令查看进程信息

查看目前系统中有哪些进程正在执行，以及它们的状态

- `ps -aux`显示的信息字段

|字段|描述|
|:-|:-|
|USER|执行进程的用户|
|PID|进程ID|
|%CPU|进程占用CPU的百分比|
|%MEM|进程占用物理内存的百分比|
|VSZ|进程占用虚拟内存的大小（kb）|
|RSS|进程占用物理内存的大小（kb）|
|TTY|终端名称|
|STAT|进程状态，S：睡眠，s：该进程是会话的先导进程，N：进程拥有比普通优先级更低的优先级，R：正在运行，D：短期等待，Z：僵死（可能进程死掉了但是内存没有释放，需要定时清除），T：被跟踪或者被停止|
|START|进程的启动时间|
|TIME|进程占用的CPU时间|
|COMMAND|执行此进程所用的命令，如果过长会截断显示，进程名称也是看这个字段|

- `ps -ef`显示的信息字段，只展示上面表格没出现过的

|字段|描述|
|:-|:-|
|PPID|父进程ID|
|C|CPU用于计算执行优先级的因子，数值越大，表示进程是CPU密集型运算，执行优先级会降低；数值越小，表示进程是I/O密集型运算，执行优先级会提高|
|STIME|进程的启动时间|

```sh
# -a：显示当前终端的所有进程信息
# -u：以用户的格式显示进程信息
# -x：显示后台进程运行的参数
# -e：显示所有进程
# -f：全格式，包括命令行
ps [-aux]

# 以全格式显示当前所有的进程
ps -ef

# 查看进程树
# -p：显示进程id
# -u：显示进程所属用户
pstree [选项]

# 查看应用有没有启动
ps -ef|grep 应用名

# 查看打开的终端
ps -aux | grep bash

# 查看远程登录服务进程，可以看到谁登录并且通过进程号终止进程进而强制用户下线
# 要是终止了sshd服务，可以通过"sudo systemctl start sshd"重启sshd服务
ps -aux | grep sshd

# grep -v "grep"：不匹配自己
ps -aux | grep sshd | grep -v "grep"
```

### top动态更新显示正在执行的进程

```sh
# -d 秒数：指定每隔几秒更新，默认3秒
# -i：不显示闲置或者僵死进程
# -p：指定进程id
top [选项]

# 在top实时监控的终端界面，可以按下一些按键进行交互
# P：按CPU使用率排序，默认
# M：按内存使用率排序
# N：按进程ID排序
# 输入u然后输入用户名然后回车，可以监视特定用户
# 输入k然后输入进程id然后回车，然后输入一个信号量（数字9）回车，可以终止该进程
# q或"Ctrl+c"：退出top
```

- top显示的内容

```sh
# 13:14:28表示当前时间
# 2:08表示系统运行时间
# 1 user表示系统用户数量
# load average: 1.11, 0.51, 0.52表示负载均衡，这3个值加起来除以3,如果在0.7以上，表示系统负载较大
top - 13:14:28 up  2:08,  1 user,  load average: 1.11, 0.51, 0.52

# 任务信息
# zombie：僵尸进程，已经死掉了但是内存没释放，需要定时清除僵尸进程
Tasks: 305 total, 1 running, 304 sleep, 0 d-sleep, 0 stopped, 0 zombie

# CPU占用百分比
# us表示用户占用的百分比
# sy表示系统占用的百分比
# id表示空闲的百分比
# wa表示CPU等待I/O的百分比
# hi表示CPU用于处理硬件中断的百分比
# si表示CPU用于处理软件中断的百分比
# st表示CPU被偷走用于运行虚拟机的百分比
%Cpu(s):  8.0 us,  6.7 sy,  0.0 ni, 82.7 id,  0.0 wa,  1.3 hi,  1.3 si,  0.0 st 

# 内存占用情况
MiB Mem :  32021.4 total,  21731.5 free,   4856.2 used,   5430.5 buff/cache     

# swap占用情况
# avail Mem表示系统在不触发 swap 的情况下，应用程序仍然可以立即使用的内存估算值，即上面的物理内存free大小
MiB Swap:      0.0 total,      0.0 free,      0.0 used.  27165.1 avail Mem

# PR：进程优先级（数值越小优先级越高）
# NI：Nice 值（影响 PR，范围 -20～19）
# VIRT：进程使用的虚拟内存总量（含代码、数据、共享库、swap 等）
# RES：Resident Memory，实际占用的物理内存（不含 swap）
# SHR：共享内存大小（共享库、共享页等）
# S：进程状态
PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
```

### iotop显示进程的I/O使用信息

```sh
sudo pacman -S iotop

sudo iotop
```

### 终止进程

```sh
# 通过进程id终止进程
# -9：强制进程立即停止
kill [选项] 进程id

# 通过进程名称终止进程，这个进程的所有子进程也会被终止
# 支持通配符，这在系统因负载过大而变得很慢时很有用
killall 进程名称
```

## 服务

服务（service）本质就是在后台运行的进程，又称为守护进程，通常会监听某个的端口，等待其它程序的请求，如mysqld、sshd、防火墙等

### 服务管理

```sh
# 在CentOS 7.0后很多服务不再使用service，而是用systemctl
# Arch Linux默认没有安装这个工具
# 启动/停止/重启/重新加载服务/查看服务状态
service 服务名 [start|stop|restart|reload|status]

# service指令管理的服务在/etc/init.d可以看到
ls -l /etc/init.d

# 查看所有服务
# Arch Linux没有这个命令工具
setup
```

### 服务的运行级别

- Linux有7种运行级别（runlevel）：常用的级别是3和5
    - 0: 系统停机状态，系统默认运行级别不能设置为0,否则不能正常启动
    - 1: 单用户工作状态，root权限，用于系统维护，禁止远程登录
    - 2: 多用户状态（没有NFS），不支持网络
    - 3: 完全的多用户状态（有NFS），登录后进入控制台命令行模式
    - 4: 系统未使用，保留
    - 5: X11控制台，登录后进入图形GUI模式
    - 6: 系统正常关闭并重启，默认运行级别不能设置为6,否则不能正常启动

- 开机流程：开机->BIOS->/boot->systemd进程->运行级别->启动运行级别对应的服务

```sh
# CentOS 7.0后
# multi-user.target表示运行级别3
# graphical.target表示运行级别5

# 查看系统默认的运行级别
sudo systemctl get-default

# 设置系统默认的运行级别
sudo systemctl set-default graphical.target
```

#### chkconfig命令

设置服务在各个运行级别下开启/关闭自启动，比如设置某个服务在运行级别3的时候自启动，在运行级别五的时候关闭自启动

Arch Linux没有这个命令工具

```sh
# chkconfig指令管理的服务在/etc/init.d可以看到
ls -l /etc/init.d

# 查看服务
chkconfig --list
chkconfig 服务名 --list

# 设置后重启生效
chkconfig --level 5 服务名 [on|off]
```

### systemctl命令

```sh
# 查看systemctl指令管理的服务
ls -l /usr/lib/systemd/system [| grep 服务名关键字]

# 注意：当服务名是xxx.service时，指定服务名时可以带或者不带.service
# 启动/停止/重启/重新加载服务/查看服务状态
systemctl <start|stop|restart|reload|status> 服务名

# 开启/关闭自启动
systemctl <enable|disable> 服务名

# 相当于执行了enable和start
systemctl enable --now 服务名

# 相当于执行了disable和stop
systemctl disable --now 服务名

# 列出所有/符合条件的服务的开机自启动状态
systemctl list-unit-files [| grep 服务名关键字]

# 查看某个服务是否开启了自启动
systemctl is-enabled 服务名
```

#### systemd环境变量

特别提一下，systemd是系统服务，不会读取普通用户的的家目录下的配置文件，如~/.bashrc

因此要么将环境变量设置到系统级配置文件中，要么通过systemd的覆盖机制来配置环境变量

```sh
# 打开服务编辑器
sudo systemctl edit 服务名

# 在编辑器里面添加环境变量，然后wq保存退出
[Service]
Environment="环境变量名1=值1"
Environment="环境变量名2=值2"

# 重新加载systemd
sudo systemctl daemon-reload

# 重启服务
sudo systemctl restart 服务名
```

## ssh

ssh：Secure Shell，是建立在应用层和传输层上的安全协议，由IETF的网络工作小组制定

ssh是目前较可靠，专为远程登录会话和其它网络服务提供安全的协议，常用于远程登录

几乎所有的Unix/Linux都可以运行ssh，要使用ssh服务，就要安装相应的服务器和客户端

- 安装ssh

```sh
# ubuntu，openssh-server实际上包含了服务器和客户端
sudo apt-get install openssh-server

# archlinux
sudo pacman -S openssh

# 启动sshd，作为服务器，作为客户端不需要
sudo systemctl start sshd

# 开机自启动sshd，作为服务器，作为客户端不需要
sudo systemctl enable sshd

# 连接
# 如果访问出现错误，尝试删除~/.ssh/known_ssh
ssh 用户名@服务器地址

# 登出方法1
exit

# 登出方法2
logout
```

## 日志

日志文件是重要的系统信息文件，其中记录了许多重要的系统事件

包括用户的登录信息、系统的启动信息、系统的安全信息、邮件相关信息、各种服务相关的信息等

日志对于安全来说也很重要，它记录了系统每天发生的各种事情

通过日志来检查错误发生的原因，或者受到攻击时攻击者留下的痕迹

简而言之，日志是用来记录重大事件的工具

- `/var/log`是系统日志文件的保存目录，日志文件如下表

|日志文件|描述|
|:-|:-|
|boot.log|系统启动日志|
|cron|与系统定时任务相关的日志|
|cpus/|记录打印信息|
|dmesg|系统在开机时内核自检的信息，可以使用dmesg命令查看内核自检信息|
|btmp|错误登录日志，此文件是二进制文件，要用lastb命令查看|
|lastlog|记录系统中所有用户最后一次登录的时间，此文件是二进制文件，要用lastlog命令查看|
|mailog|记录邮件信息|
|message|记录系统重要消息，如果系统出现问题，首先要检查的就是这个文件|
|secure|记录验证和授权的信息，只要涉及账号和密码都会记录，如系统登录、ssh登录、su切换用户、sudo授权，甚至添加用户和修改用户密码都会记录在这个日志文件中|
|wtmp|永久记录所有用户的登录、注销信息，同时记录系统的启动、重启和关机事件，此文件是二进制文件，要用last命令查看|
|ulmp|记录当前已登录的用户的信息，这个文件会随着用户的登录和注销不断变化，只记录当前登录用户的信息，要用w、who、users等命令查看|

### 日志管理服务

```sh
# CentOS 7.6, 查看系统的rsyslog服务是否已经启动
ps -aux | grep rsyslog

# CentOS 7.6, 查看rsyslog服务是否开启了自启动
sudo systemctl list-unit-files | grep rsyslog
```

#### rsyslog服务配置文件

rsyslog服务配置文件：`/etc/rsyslog.conf`，配置什么日志写到什么日志文件

格式`*.*`，第一个`*`表示日志类型，第二个`*`表示日志级别

- 日志类型见下表

|日志类型|描述|
|:-|:-|
|auth|pam产生的日志|
|authpriv|ssh、ftp等登录信息的验证信息|
|corn|时间任务相关|
|kern|内核|
|lpr|打印|
|mail|右键|
|`mark(syslog)-rsyslog`|服务内部的信息，时间表示|
|news|新闻组|
|user|用户程序产生的相关信息|
|uucp|unix to unix copy主机之间相关的通信日志|
|`local 1-7`|自定义的日志设备|

- 日志级别见下表

|日志级别|描述|
|:-|:-|
|debug|包含调试信息，日志信息最多|
|info|一般信息，最常用|
|notice|最重要的普通信息|
|warning|警告信息|
|err|错误级别，阻止某个功能或者模块不能正常工作的信息|
|crit|严重级别，阻止整个系统或者整个软件不能正常工作的信息|
|alert|需要立刻修改的信息|
|emerg|内核崩溃等重要信息|
|none|什么都不记录|

```sh
# 自定义日志服务
# 增加一行：*.*                /var/log/handle.log
# 如果没有/var/log/handle.log会自动创建，自己创建要设置读写权限
# 然后重启，就可以看到日志了
vim /etc/rsyslog.conf
```

#### rsyslog服务记录的日志文件

- 日志文件格式包含以下4列
    - 产生事件的时间
    - 产生事件服务器的主机名
    - 产生事件的服务名或程序名
    - 事件的具体信息

### 日志轮替

就是把酒的日志文件移动并改名，同时新建的空日志文件，当旧日志文件超出保存的范围后，就会进行删除

- 日志轮替文件命名
    - centos7用logrotate进行日志轮替管理，想要改变日志轮替文件名字，通过修改/etc/logrotate.conf中“ddateext”参数
    - 如果配置文件中有“ddateext”参数，就会用日期作为日志文件后缀，例如secure-20260108, 这样日志文件名就不会重叠，也就不需要日志文件的改名，只需要指定保存日志的个数，删除多余的日志文件即可
    - 如果配置文件中无“ddateext”参数，日志文件就需要改名了。第一次轮替时，当前的secure日志自动改名为secure.1，然后新建secure日志；第二次轮替时，secure.1自动改名为secure.2, 当前的secure日志自动改名为secure.1，然后新建secure日志；以此类推

- 把自己的日志加入日志轮替
    - 方法1：直接在/etc/logrotate.conf中写入该日志的轮替策略
    - 方法2: 在/etc/logrotate.d中创建日志轮替文件，并写入该日志的轮替策略（推荐）

```conf
# 把/etc/logrotate.d这个目录中所有的子配置文件（也算是单独设置）读取进来
include /etc/logrotate.d

# 单独设置比全局设置的优先级更高
# 下面的写法可以作为单独的子配置文件（如bootlog）内容或者追加在/etc/logrotate.conf中
/var/log/wtmp {
    # 每月堆日志文件进行一次轮替
    monthly
    # 建立新的日志文件，权限：0664，所有者：root，所在组：utmp
    create 0664 root utmp
    # 最小轮替大小，日志超过这个值才会轮替，否则就是时间到了一个月，也不进行日志转储
    minsize 1M
    # 仅保留一个日志备份: 即只有wtmp和wtmp.1
    rotate 1
}
/var/log/btmp {
    # 如果日志不存在，则忽略该日志的警告信息
    missingok
    monthly
    # ...
}
```

- /etc/logrotate.conf文件参数

|参数|描述|
|:-|:-|
|daily|每天轮替|
|weekly|每周轮替|
|monthly|每月轮替|
|rotate 数字|保留的日志文件个数，0表示不备份|
|compress|日志轮替时，压缩旧日志|
|create mode owner group|创建新日志，同时指定新日志的权限、所有者和所在组|
|mail address|轮替时，输出内容发送到指定邮件|
|missingok|如果日志不存在，则忽略该日志的警告信息|
|notifempty|如果日志为空文件，则不进行日志轮替|
|minsize|日志轮替的最小值，达到这个值，并且到了轮替时间，才会轮替|
|size|按指定大小进行日志轮替，而不是按时间|
|dateext|用日期作为日志轮替文件的后缀|
|sharedscripts|在此关键字之后的脚本只执行一次|
|prerotate/endscript|在日志轮替之前执行脚本|
|postrotate/endscript|在日志轮替之后执行脚本|

- 日志轮替原理：在/etc/cron.daily目录，有一个可执行文件logrotate，定时任务执行它来实现日志轮替

### 内存日志

内存日志重启会清空

```sh
# 查看内存日志
# -n 3：查看最新3条
# --since 19:00 --until 19:10：查看起始时间至结束时间的日志，可加秒和日期
# -p err：查看报错日志
# -o verbose：查看详情
# _PID=进程id _COMM=sshd：查看包含进程id和sshd的日志，也可以用grep筛选
# -u 是 --unit 参数的缩写，指定Systemd单元（Unit），Systemd 使用“单元（Unit）”来管理和组织各种服务、挂载点、定时任务等
journalctl | grep sshd


# 标准命令
journalctl -u 服务名

# 实时追踪日志（推荐用于排查启动问题）
journalctl -u 服务名 --no-pager --follow --pager-end
```

## vi/vim编辑器

vim就是vi的增强版

```sh
# ubuntu
sudo apt-get install vim
```

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
|18|`Ctrl + Shift + V`|粘贴剪切板的内容|

## Shell

Shell是一个命令解释器，解释执行用户所输入的命令和程序

Shell为用户提供了一种命令行的接口，接收用户的键盘输入，并分析和执行输入字符串中的命令，然后返回执行结果

### 常用命令

```sh
# 查看当前用户Shell类型
echo $SHELL

# 查看帮助或保留关键字
help

# 不同版本的 bash 在某些特性上会有差异
# 可以先查看bash版本，再查询支不支持某个语法
bash --version
```

### 创建第一个Shell脚本文件

- Shebang的两种写法，注意#和!和指定路径中间是没有空格的

```sh
#!/bin/bash
# 方法1，直接指定bash路径，路径固定，不容易被中间人攻击或环境变量欺骗

#!/usr/bin/env bash
# 方法2，通过env查找bash，可移植，适合不同系统（如macOS/Linux），如果$PATH被恶意篡改，可能调用到伪造的bash
```

- 新建文件并输入内容

```sh
#!/bin/bash
echo "Hello World!"

# 严格模式：遇错退出、未定义变量报错、管道失败报错
# 启用选项：set -o <选项名>
# 禁用选项：set +o <选项名>
# -e：errexit，任何命令返回非零退出状态时立即终止脚本执行，如果自定义方法有非零值返回并且不想终止脚本，需要在方法中局部关闭或做其它处理
# -u：nounset，引用未定义变量时视为错误并退出
# -o pipefail，管道命令中任一命令失败则整体返回失败状态
set -euo pipefail  

# 第一行"#!/bin/bash"，告诉内核这是一个脚本文件，用"#!"后面的"/bin/bash"解释器来执行它
# 如果没有这一行，系统会用默认shell（通常是/bin/sh）
# 但"/bin/sh"只是一个“指向某个 shell 的符号链接（symlink）”，而不同Linux发行版会把它指向不同的shell
# 如CentOS/RHEL默认shell是bash，Debian/Ubuntu的默认shell是dash（功能比bash少很多）
# 如果不写第一行，并且使用了默认shell不支持的语法，则运行将会报错
```

- 赋予脚本文件执行权限

```sh
chmod u+x /path/to/file.sh
```

- 执行脚本

```sh
# 如果脚本文件是在当前目录下，命令必须是"./file.sh"，而不能是"file.sh"
# 因为输入"file.sh"并回车后，shell会按顺序搜索PATH环境变量中的目录
# 但是当前目录"."默认不在PATH中（出于安全考虑），所以直接输入"file.sh"时，shell找不到它
# 而"./file.sh"则是明确告诉了shell去指定的目录找到这个文件并执行它
/path/to/file.sh

# 以后台的方式运行
/path/to/file.sh &

# 脚本没被赋予执行权限的执行方式
sh /path/to/file.sh
```

### Shell语法

#### 注释

```sh
# 单行注释
# 注释内容

# 多行注释
:<<!
注释内容
!
```

#### Shell变量

Shell变量分为系统变量（所有环境变量都是系统变量）和用户自定义变量

- 用户自定义变量

```sh
# 定义和使用用户自定义变量
# 变量名不能以数字开头，规范为大写
# 赋值语句=左右不能有空格
变量名=变量值

# 使用变量，为了规范，变量作为命令参数时一致加双引号
echo "$变量名"

# 撤销（销毁）变量
unset 变量名

# 定义只读变量（不能unset）
readonly 变量名=变量值

# 将命令的结果赋给变量的两种写法
# $(具体命令)：把命令的输出作为字符串返回
变量名=`具体命令`
变量名=$(具体命令)

# 将算术运算的结果赋给变量
# $((运算表达式))：执行算术运算并返回结果
变量名=$((运算表达式))
```

- 系统变量

```sh
# 显示当前shell中所有变量
set
```

#### 位置参数变量

|语法|描述|
|:-|:-|
|`$n`或`${n}`|当n=0-9时，用`$n`，`$0`表示命令本身，`$1-$9`表示第1-第9个参数;当n>=10,用`${n}`|
|`$*`|表示命令行中的所有参数，并且把所有的参数看成一个整体|
|`$@`|表示命令行中的所有参数，但是把每个参数区分对待|
|`$#`|表示命令行中所有参数的总个数|

```sh
# 当执行下面的脚本时，如果希望获取到命令行的参数信息，就可以用到位置参数变量了
sh /path/to/file.sh p1 p2 pn

# 比如想在/path/to/file.sh中输出第一个参数的值可以这样写
echo "$1"

# 所有参数作为整体在一行中全部输出
# 但是如果不加引号，则跟下面的例子一样
for item in "$*"; do
  echo "$item"
done

# 有多少个参数输出多少行
for item in "$@"; do
  echo "$item"
done
```

#### 预定义变量

预定义变量就是shell设计者事先已经定义好的变量，可以直接在shell脚本中使用

|语法|描述|
|:-|:-|
|`$$`|表示当前进程的进程id|
|`$!`|表示后台运行的最后一个进程的进程id|
|`$?`|表示最后一次执行的命令的返回值，如果为0,说明上一个命令正确执行；如果为非0, 说明上一个命令执行不正常|

#### 字符串

```sh
# 可以用单引号或双引号定义字符串
# 单引号中的特殊字符被视为字面量，双引号中的特殊字符会被转义
# 当字符串不包含特殊字符（如空格 $, *, !, ?, (), {}, [] 等），可以不加引号，但是不推荐
name='Handle'
hello="Hello, $name!"

# 拼接字符串
hello="Hello, $name!"
hello="Hello, ${name}!"
hello="Hello, "$name"!"
hello='Hello, '$name'!'

# 获取字符串长度的两种方式
# bash 内置功能，性能更好
echo ${#name}
# 外部命令（性能较差）
expr length "$name"

# 截取字符串
# 从字符串第0个字符开始往后截取2个字符（索引从 0 开始）
# Ha
echo ${name:0:2}
```

#### 数组

```sh
# 只支持一维数组，不限定数组大小
# 创建数组
array=(1 2 3)

# 获取数组长度的两种方式
echo ${#array[@]}
echo ${#array[*]}

# 获取数组第0个元素
echo ${array[0]}

# 删除数组第0个元素，这时候如果"echo ${array[0]}"将输出空行，不会报错
unset array[0]

# 删除整个数组
unset array

# 遍历数组
# 删除元素后，如果使用 ${array[0]} 访问会得到空值
# 遍历数组时建议使用 "${!array[@]}" 获取有效索引，或使用 "${array[@]}" 直接遍历值
for item in ${array[@]}; do
    echo $item
done
```

#### 运算符

##### 算术运算符

|运算符|描述|
|:-|:-|
|`+`|加|
|`-`|减|
|`*`|乘|
|`/`|除|
|`%`|取余|
|`=`|赋值|
|`==`|相等|
|`!=`|不相等|

```sh
# 写法1，现代bash的标准算术扩展，功能强、语法安全，推荐
# 表达式左右不强制要求有空格
$((运算表达式))

# 例
sum=$((2 * 3))

# 写法2，旧语法
$[运算表达式]

# 写法3,
# 注意这种写法运算表达式中的运算符左右必须包含空格
# 如果不包含空格，将会输出表达式本身
# 对于某些运算符，还需要用\进行转义，不然会报错
expr 运算表达式

expr 1 + 2
expr 1 \* 2
expr 1 / 2

# 输出算术运算的结果
echo `expr 1 + 2`
```

##### 关系运算符

|运算符|描述|
|:-|:-|
|`-eq`|两个数是否`相等`|
|`-ne`|两个数是否`不等`|
|`-lt`|左边的数是否`小于`右边的|
|`-gt`|左边的数是否`大于`右边的|
|`-le`|左边的数是否`小于等于`右边的|
|`-ge`|左边的数是否`大于等于`右边的|

```sh
a=1
b=2
if [[ $a -eq $b ]]
then
   echo "equal"
else
   echo "not equal"
fi
```

##### 逻辑运算符

|运算符|描述|
|:-|:-|
|`&&`|逻辑与，运算符连起来的两条命令，左边的执行结果为true才会执行右边的|
|`\|\|`|逻辑或，运算符连起来的两条命令，左边的执行结果为false才会执行右边的|

##### 布尔运算符

|运算符|描述|
|:-|:-|
|`-a`|与|
|`-o`|或|
|`!`|非|

##### 字符串运算符

|运算符|描述|
|:-|:-|
|`=`|两个字符串是否相等，兼容所有 Shell，最安全、最规范|
|`==`|两个字符串是否相等，Bash专属语法，语义更直观，且支持模式匹配|
|`!=`|两个字符串是否不等|
|`-z`|字符串长度为0返回true|
|`-n`|字符串长度不为0返回true|
|`str`|字符串不为空返回true|

```sh
#!/bin/bash
a="a"
b="b"
if [[ $a == $b ]]
then
   echo "equal"
else
   echo "not equal"
fi
```

##### 文件相关运算符

![文件相关运算符](/images/文件相关运算符.png)

|运算符|描述|
|:-|:-|
|`-e`|文件/目录是否存在|
|`-f`|是否为普通文件（既不是目录也不是设备文件）|
|`-d`|是否为目录|
|`-s`|文件是否为空（文件大小大于0返回true）|
|`-b`|是否为块设备|
|`-c`|是否为字符设备|
|`-p`|是否为有名管道|
|`-r`|是否可读|
|`-w`|是否可写|
|`-x`|是否可执行|
|`-u`|文件是否设置了SUID|
|`-g`|文件是否设置了SGID|
|`-k`|文件是否设置了粘着位 (Sticky Bit)|

```sh
file="/path/to/file.txt"
if [[ -e $file ]]
then
   # todo
fi
```

#### 条件判断

```sh
# 写法1，推荐
# bash专用的增强条件表达式，一般用于字符串/文件判断
# 注意条件表达式左右要有空格
[[ 条件表达式 ]]

# 写法2，功能弱、容易踩坑
# 注意条件表达式左右要有空格
# [  ]返回false，条件表达式非空就返回true
[ 条件表达式 ]

# 字符串判断
if [[ "a" = "a" ]]; then
    echo "a equals a"
else
    echo "a does not equal a"
fi

# 整数判断1
if [[ 1 -lt 2 ]]; then
    echo "1 < 2"
fi

# 整数判断2
# 内部变量需要"$"
a=1
b=2
if [[ $a -lt $b ]]; then
    echo "$a < $b"
fi

# 逻辑运算
if [[ 3 -eq 3 ]] && [[ 5 -lt 10 ]]; then
  echo "all true"
fi

# 判断文件是否存在
if [[ -f /home/handle/Downloads/xxx.png ]]; then
    echo "File exists."
else
    echo "File does not exist."
fi

# 如果不用if语句可以这样写
dir="/home/handle/mydir"
[[ ! -d "$dir" ]] && echo "$dir does not exist"
```

#### 流程控制

##### `if`语句

```sh
# 注意条件表达式左右要有空格
if [[ 条件表达式1 ]]; then
    命令1
elif [[ 条件表达式2 ]]; then
    命令2
else
    命令3
fi

# if后也可以跟命令，command的执行结果（command的退出状态码）：0 → 条件为真，非0 → 条件为假
if command; then
    命令1
elif command; then
    命令2
else
    命令3
fi

# 空语句1（什么都不做）
if [[ condition ]]; then
    :
fi

# 空语句2（什么都不做）
if [[ condition ]]; then
    true
fi
```

##### for语句

```sh
# 遍历现成的一组值1
for item in 1 2 3; do
    echo "$item"
done

# 遍历现成的一组值2
# 当这组值有规律时用两个点省略中间部分
for item in {1..3}; do
    echo "$item"
done

# 数字循环1，for的(())中引用变量不需要加$
length=3
for ((i=0; i<length; i++)); do
    echo "$i"
done

# 数字循环2，for的(())中引用变量不需要加$
sum=0
for ((i = 1; i <= 100; i++)); do
  sum=$((sum + i))
done
echo "The sum of numbers from 1 to 100 is: $sum"

# 遍历数组
array=(1 2 3)
for item in ${array[@]}; do
    echo "$item"
done
```

##### while语句

```sh
i=0
while ((i<3)); do
    echo "$i"
    ((i++))
done
```

##### case语句

```sh
# 笔者试过了变量值是整数或者字符串时不加双引号也可以，但是还是按规范来吧
case $变量名 in
"变量值1")
    # to do
    ;;
"变量值2")
    # to do
    ;;
"变量值n")
    # to do
    ;;
*)
    # default to do
    ;;
esac

# 当执行脚本文件的命令行参数是1时，输出：today...
case $1 in
"1")
    echo "today"
    ;;
"2")
    echo "tomorrow"
    ;;
*)
    echo "other day"
    ;;
esac
```

#### read读取控制台输入

```sh
# -p：提示
# -t：读取等待时间，超时后不再等待输入
# -r：禁止反斜杠转义，将输入内容当成字面量，提高安全性
read [选项] 存放输入内容的变量名

read -p "Enter your name: " name
echo "Hello, $name!"
```

#### 函数

函数分为系统函数（可以直接使用了）和自定义函数

##### 系统函数

```sh
# 返回字符串最后一个/后面的部分，常用于获取文件名
# 如果指定了后缀，则将字符串的后缀也去掉
basename 字符串 [后缀]

# 例子
basename /home/handle/Documents/file.txt .txt

# 返回字符串最后一个/前面的部分，常用于获取文件所在目录
dirname 字符串

# 例子
dirname /home/handle/Documents/file.txt
```

##### 自定义函数

```sh
# 定义函数方法1，推荐
函数名(){
    # to do
    # 定义作用域为方法内部的局部变量，方法执行结束变量就会销毁
    local 变量名=值
    # return 只能返回退出码（0-255）
    [return int]
}

# 定义函数方法2
function 函数名 {
    # to do
    # return 只能返回退出码（0-255）
    [return int]
}

# 调用函数
函数名 [参数1 参数2 ... 参数n]

# 定义无参数无返回值函数1
greet(){
    echo "Hello World!"
}

# 调用函数
greet


# 定义带参数无返回值函数2
sum(){
    # $1 $2 $3 …，表示第一个、第二个、第三个...参数
    # $0表示脚本本身的名称
    # `$1-$9`表示第1-第9个参数;当n>=10,用`${n}`
    # $10会被解析为$1和字面量0的拼接，而非第十个参数
    # $@，表示所有参数
    # $#，表示参数数量
    # 不推荐写成：echo `expr "$1" + "$2"`
    # 原因：expr是外部命令，性能差，还有就是expr的语法没有下面的写法灵活，容易犯错
    echo "$(($1 + $2))"
}

# 调用函数
sum 1 2

# 定义有参有返回值函数3
# return 只能返回退出码（0-255）
# 如果想要返回字符串或数字，必须用echo，或定义全局变量然后在函数里面修改它的值（不推荐），或使用命令替换
# 推荐使用echo+命令替换
sum(){
    echo "$(($1 + $2))"
}

# 调用函数并获取返回值
result=$(sum 1 2)
echo "$result"
```

- `$*`与`$@`的区别

|表达式|未引用|双引号包裹|
|:-|:-|:-|
|`$*`|展开为所有参数|展开为单个字符串（所有参数合并）|
|`$@`|展开为所有参数|展开为独立的参数（每个参数保持独立）|

在传递参数时，始终使用`$@`以确保每个参数的独立性（特别是当参数包含空格时）

#### 追加内容到文件

```sh
FILE="/path/to/file"

# 追加一行配置
echo "content of line" >> "$FILE"

# 追加多行配置，EOF可以换成任何你喜欢的标记，比如END
cat <<EOF >> "$FILE"
content of line1
content of line2
EOF
```

#### 设置命令/脚本执行超时时间

```sh
# 单位：秒
timeout 60 /path/to/file.sh

timeout 60 命令
```

#### 防止脚本文件并发执行

- 固定文件描述符写法

```sh
#!/usr/bin/env bash

# 1.打开锁文件并绑定到文件描述符200（钥匙），指向同一个锁文件
exec 200>/var/lock/myapp.lock

# 2.尝试加锁
# -n 表示非阻塞，如果锁（文件）被别人拿了，就立刻失败并执行后面的 echo 和 exit
flock -n 200 || { echo "另一个实例正在运行，脚本退出！"; exit 1; }

# 3.如果成功走到这里，说明加锁成功，可以安全地执行核心业务逻辑了
# todo

# 4.脚本结束时，文件描述符会自动关闭，锁也会随之自动释放
```

- 自动分配文件描述符写法

```sh
#!/usr/bin/env bash

# 1.让Bash自动分配一个文件描述符（钥匙），指向同一个锁文件
exec {mylock}>/var/lock/myapp.lock

# 2.尝试加锁
# -n 表示非阻塞，如果锁（文件）被别人拿了，就立刻失败并执行后面的 echo 和 exit
# 注意：这里必须使用 "$mylock" 变量，因为它里面存着系统分配的实际数字
flock -n "$mylock" || { echo "另一个实例正在运行，脚本退出！"; exit 1; }

# 3.如果成功走到这里，说明加锁成功，可以安全地执行核心业务逻辑了
# todo

# 4.脚本结束，进程退出。
# 此时系统会自动回收该进程的文件描述符（钥匙被收回），
# 内核检测到没有进程持有这把钥匙了，就会自动把门（锁文件）打开
```

#### 综合

```sh
# test.txt文件第二列求和
cat /path/to/test.txt | awk -F " " '{sum+=$2} END {print sum}' 

# 统计/home/test目录下所有文件个数和所有文件总行数
find /home/test -name "*.*"
find /home/test -name "*.*" | xargs wc -l

# 备份/home/handle目录到/home/test下，按备份时间生成备份包
tar zcvf /home/test/handle-`date +%Y-%m-%d_%H%M%S`.tar.gz /home/handle

# 判断有效用户id是否为root
if [[ $EUID -ne 0 ]]; then
    echo "Please run as root"
    exit 1
fi
```

## 备份和恢复

- 备份和恢复的两种方式
    - 1.把需要的文件/分区用tar打包，恢复的时候解压覆盖就行
    - 2.用dump和restore命令

### dump

- 红帽Linux

```sh
yum -y install dump
yum -y install restore
```

```sh
# dump支持分卷和增量备份
# 通过dump命令，配合crontab，可以实现无人值守备份
dump [-cu] [-123456789] [-f 生成的备份文件名] [-T 日期] [目录或文件系统]

# 例1，将/boot分区所有内容备份到/opt/boot.bak.bz2中，备份层级为0
dump -0uj -f /opt/boot.bak0.bz2 /boot

# 例2, 备份/boot分区，备份层级为1（只备份上次使用层级0备份后发生过改变的数据）
dump -1uj -f /opt/boot.bak1.bz2 /boot

# 例3, 备份/etc目录
dump -0j -f /opt/etc.bak.bz2 /etc

# 查看备份的分区及最后一次备份的层级、日期时间
dump -W
 
# 查看备份时间文件
cat /etc/dumpdates
```

- dump命令选项参数

|选项|描述|
|:-|:-|
|-c|创建新的归档文件，并将由一个或多个文件参数所指定的内容写入归档文件的开头|
|-0123456789|备份的层级，分区备份才可以用，目录/文件备份不可以（只能用层级0），0为完整备份，若指定0以上的层级，则备份至上一次备份以来修改或新增的文件，到9后，可以再次轮替（层级又从0开始）|
|-f 文件名|指定生成的备份文件名|
|-j|调用bzlib库压缩备份文件（bz2格式，文件更小）|
|-T 日期|指定开始备份的日期时间|
|-u|备份完成后，在/etc/dumpdates中记录备份的文件系统、层级、日期时间等|
|-t|指定文件名，若该文件已经在备份文件中，则列出名称|
|-W|显示需要备份的文件及最后一次备份的层级、时间、日期|
|-w|与-W类似，但只显示需要备份的文件|

### restore

```sh
# 恢复已备份的文件，如从dump生成的备份文件中恢复原文件
# 模式只能选择一种
restore [模式] [选项]

# 例1
restore -r -f /opt/etc.bak.bz2

# 例2, 如果有增量备份，需要把增量备份文件按循序进行恢复
restore -r -f /opt/boot.bak0.bz2
restore -r -f /opt/boot.bak1.bz2
restore -r -f /opt/boot.bak2.bz2
restore -r -f /opt/boot.bakn.bz2
```

|模式|描述|
|:-|:-|
|-C|对比模式，将备份的文件与已存在的文件相互对比|
|-i|交互模式，在进行还原操作时，依序询问用户|
|-r|还原模式|
|-t|查看模式，看备份文件包含哪些文件|

|选项|描述|
|:-|:-|
|-f 备份文件|从指定的备份文件中读取备份数据，进行还原|

## 系统管理工具

### webmin

webmin是基于web的功能强大的unix/linux系统管理工具，管理员可通过浏览器访问webmin的各种管理功能并完成相应的管理操作

```sh
# 安装，noarch表示通用版
rpm -ivh webmin-xxx.noarch.rpm

# 修改webmin的root用户密码
/usr/libexec/webmin/changepass.pl /etc/webmin root 具体密码

# 修改webmin服务的端口号（默认10000）
# port和listen都改，改完后防火墙开放相应端口
vim /etc/webmin/miniserv.conf

# 启动/重启/停止
/etc/webmin/start
/etc/webmin/restart
/etc/webmin/stop

# 登录，输入root账号和你的密码
# 首先要进行IP访问控制设置
http://ip:port
```

### bt（宝塔）

bt是提升运维效率的linux服务器管理软件（有些功能要付费）

支持一键LAMP/LNMP/集群/监控/网站/FTP/数据库/Java/等多项服务器管理功能

```sh
# 安装完成控制台会打印访问的外网/内网地址和用户名/密码，可以在网页面板进行修改
# 进入网页要手机号注册
# 如果忘记了bt的外网/内网地址和用户名/密码，可以这样查看
bt default
```

## 系统启动流程

- 1.自检，检查硬件设备有没有故障
- 2.如果有多块启动盘的话，需要在BIOS中选择启动磁盘
- 3.启动MBR中的bootloader引导程序
- 4.加载内核文件，两个关键文件如下
    - 4.1 kernel文件：vmlinuz-xxx
    - 4.2 initrd文件：initramfs-xxx.img
- 5.执行所有进程的父进程：systemd
- 6.欢迎界面

### CentOS7启动流程

![CentOS7启动流程](/images/CentOS7启动流程.png)

![CentOS7启动流程2](/images/CentOS7启动流程2.png)

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

### RPM包管理器

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

### yum包管理器

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

## Ubuntu

- 查看系统版本信息

```sh
# 简要信息
lsb_release -a

# 详细信息
cat /etc/os-release
```

### apt包管理器

apt：Advanced Packaging Tool

#### 修改apt下载源

- 打开镜像站，如<https://mirrors-i.tuna.tsinghua.edu.cn/>

- 搜索ubuntu，然后点击问号，根据镜像站的说明修改

```sh
# 查看ubuntu版本
cat /etc/os-release

# 先备份（24.04及以后版本）
sudo cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.bk

# 修改下载源，一般是清空配置文件里面的内容然后粘贴镜像源的内容进来
sudo vim /etc/apt/sources.list.d/ubuntu.sources

# 然后更新源
sudo apt-get update
```

#### apt常用命令

- apt：交互式常用，命令可能会变化
- apt-get/apt-cache：脚本、自动化、长期兼容性使用

```sh
# 更新源
sudo apt-get update

# 安装包
# -f：修复安装
# --reinstall：重新安装包
sudo apt-get [-f] install 包名 [--reinstall]

# 删除包
# --purge：删除配置文件
sudo apt-get remove 包名 [--purge]

# 安装相关的编译环境
sudo apt-get build-dep 包名

# 下载该包的源代码
sudo apt-get source 包名

# 更新已经安装的包
sudo apt-get upgrade

# 升级系统
sudo apt-get dist-upgrade

# 搜索包
sudo apt-cache search 包名

# 查看包信息
sudo apt-cache show 包名

# 查看该包依赖哪些包
sudo apt-cache depends 包名

# 查看该包被哪些包依赖
sudo apt-cache rdepends 包名

# 查看手动安装的包
apt list --manual-installed
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
# --disable-download-timeout：use relaxed timeouts for download
# --noconfirm：静默安装，不询问
# --needed：do not reinstall up to date packages
sudo pacman -S [--disable-download-timeout] [--noconfirm] [--needed] 包名 [包名2 包名3 ...] 

# 安装本地.pkg.tar.zst安装包或.pacman安装包
sudo pacman -U /path/to/pkg.tar.zst安装包或.pacman安装包

# 卸载软件
# R：删除指定包
# s：删除不需要的依赖（仅限于没有其他包需要的依赖）
# n：删除配置文件
sudo pacman -Rns package_name

# 搜索远程仓库的某个软件
sudo pacman -Ss 软件关键字

# 搜索已安装的某个软件
sudo pacman -Qs 软件关键字

# 查看已安装的所有软件
sudo pacman -Q

# 列出该包安装的所有文件及其路径
sudo pacman -Q 包名

# 列出未安装包的文件列表
sudo pacman -Ql -p 包名.pkg.tar.zst

# 查询文件属于哪个软件包
sudo pacman -Qo 文件名

# 列出包组的所有应用
sudo pacman -Sg kde-applications

# 选择安装包组里面的应用，默认全部安装，可以根据交互选择应用对应的数字，如果安装多个用空格隔开，来自定义安装
sudo pacman -S kde-applications

# 查看包组中还没安装的软件
sudo pacman -Sg plasma | grep -v " $(sudo pacman -Qg)"

# pactree命令工具包，根据需要安装
sudo pacman -S pacman-contrib

# 显示包的依赖树，只显示直接依赖
pactree -d1 pinta
```

### .AppImage软件包

下载.AppImage格式的软件包后，先赋予该软件包执行权限，第一次先通过终端启动

如果运行异常，根据终端提示安装相应的依赖就可以了

```sh
# 比如有些AppImage软件包是需要fuse2的
sudo pacman -S fuse2
```

- 复制软件包里面的图标文件

```sh
# 需要先赋予appimage文件执行权限
# 执行这个命令后会输出一个挂载路径，如：/tmp/.mount_xxxxx
/path/to/file.AppImage --appimage-mount

# 然后新开一个终端并进入这个路径
cd /tmp/.mount_xxxxx

# 找到图标文件，比如可能在：/tmp/.mount_xxxxx/usr/share/icons/hicolor/512x512/apps/file.png
# 然后复制到一个可以找得到的路径下
# 最后关闭这个终端，以及在最开始的那个终端Ctrl+C，结束挂载
```

### .run软件包

.run软件包的卸载不好说，根据具体软件具体分析，下面的例子使用的是virtualbox

十分不推荐自己安装.run的软件包

```sh
# 给.run软件包执行权限
chmod +x /path/to/软件包

# 执行安装，可能需要root权限
sudo /path/to/软件包

# 卸载
sudo /path/to/软件包 uninstall
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

#### 制作U盘启动

- 如果是在windows系统制作，下载Rufus（Portable版本，免安装）：<https://rufus.ie>
- 如果是在Linux系统制作，命令行，步骤如下：

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
sudo dd bs=4M if=/path/to/archlinux-version-x86_64.iso of=/dev/disk/by-id/usb-My_flash_drive conv=fsync oflag=direct status=progress

# 如果没有安装dd，可以直接用cp命令，但是不能设置每次的读写大小
sudo cp /path/to/archlinux-version-x86_64.iso /dev/disk/by-id/usb-My_flash_drive
```

- 使用Ventoy，Windows和Linux都有安装包（推荐）

##### Ventoy

官网：<https://github.com/ventoy/Ventoy>

Ventoy可制作同时支持Windows、Linux等多操作系统的U盘启动，并且多余的U盘空间还可以存放其它任意文件，实乃上上之选

- 下载ventoy-version-linux.tar.gz，并校验哈希码，然后解压

- 确保系统安装了dosfstools

- 进入ventoy根目录

```sh
# 命令行启动
Ventoy2Disk.sh

# GUI启动，推荐
VentoyGUI.x86_64

# 网页版启动
WebUI/index.html
```

- 关于Secure Boot Support，保持默认勾选就行
    - 勾选了Secure Boot Support，如果主板的Secure Boot是开启的，那么第一次U盘启动，需要注册密钥，根据提示操作就行
    - 无论勾选不勾选Secure Boot Support，只要把主板Secure Boot关掉，U盘启动都一定能正常启动

- U盘启动制作完成后，会看到Ventoy创建了两个分区，默认名字为Ventoy和VTOYEFI
    - 把系统镜像（iso文件）复制到Ventoy里面就行（里面任意目录下都可以），当然也可以存放其它你想存到U盘的任何文件
    - VTOYEFI分区里放的是Ventoy的引导程序，不要动它

- 如果提示安全引导违规，关闭BIOS的安全启动模式，保存重启，不要按ESC，因为会弹出一个确认是否真的要关闭安全引导的交互，选确认

- 启动的时候，手动选择UEFIxxx的引导选项，默认自动进来的话不是以UEFI模式启动的

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

除非考虑兼容老设备，不然首选GPT分区

|分区表|结构|支持的最大磁盘容量|支持分区数量|启动方式|可靠性与安全性|分区表占用空间|
|:-|:-|:-|:-|:-|:-|:-|
|MBR|1983 年的老设计，限制较多|2TB|最多4 个主分区（或3主分区 + 1扩展分区）|BIOS 启动|没有备份，损坏后难恢复|~1MB|
|GPT|UEFI 时代的新标准，功能更强|理论支持9.4ZB（几乎无限）|默认支持 128 个分区（Windows 下）|UEFI 启动（Windows 11 必须）|有备份分区表 + CRC校验|~2–4MB|

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
# -n，标签
mkfs.fat -F 32 [-n boot] /dev/efi_system_partition

# 格式化swap分区
# -L，标签
mkswap [-L swap] /dev/swap_partition

# 格式化/分区为ext4
# -L，标签
mkfs.ext4 [-L archlinux] /dev/root_partition
```

##### 3. 挂载分区

```sh
# 要先挂载根分区
mount /dev/root_partition /mnt

# 挂载efi系统分区，使用--mkdir创建/mnt/boot目录，并将efi分区挂载在/mnt/boot目录下
# --mkdir是可以生成多级目录的
mount --mkdir /dev/efi_system_partition /mnt/boot

# 挂载swap分区，挂载后，swap对live系统也是有用的，而不仅仅是给新系统用的噢
swapon /dev/swap_partition

# 如果给swap分区设置了label，也可以通过label挂载
swapon /dev/disk/by-label/swapLabelName

# 如果有其它硬盘想要永久挂载，也可以这时候挂载
# 后面执行genfstab -U /mnt >> /mnt/etc/fstab时就会一起写到fstab了
mount --mkdir /dev/other_disk_partition /mnt/mount_point
```

#### 安装基础系统包

##### 配置pacman镜像源(可选)

非常建议配置，不然安装会很慢

```sh
# 用vim或nano编辑文件/etc/pacman.d/mirrorlist
vim /etc/pacman.d/mirrorlist

# 在文件中查询国内镜像源，并复制该行，粘贴到文件其他源的最前面
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

##### 用pacstrap安装基础系统包

pacstrap是Arch Linux安装环境提供的一个脚本，用来在目标挂载点（通常是新系统的根目录，比如 /mnt）安装基础系统包

pacstrap通常只用来安装基础系统包，安装完之后，可以用genfstab和arch-chroot进入新系统对其进行配置

###### 物理机安装

```sh
# base，linux，linux-firmware分别是基础包组，linux内核和驱动程序
# Arch Linux官方提供了linux，linux-lts，linux-zen，linux-hardened内核包，但是对于初学者，只推荐使用linux内核
# linux：主线稳定版，更新快，更新出问题有可能回归更新，整体性能优秀
# linux-lts：长期支持版，稳定性最好
# linux-zen：桌面优化版，游戏表现最好，能有更丝滑的交互体验，但是游戏表现没提升多少，特别是吃显卡的游戏，总体比linux包好一丢丢
# linux-hardened：安全强化版，安全性最高
# 注意：如果后期要安装headers包，需要根据安装的内核包选择对应的headers包进行安装
# 如linux包选择linux-headers，linux-lts包选择linux-lts-headers
# 如果前期只安装了一种内核包，后期想再安装另一种内核包，安装完后需要执行"sudo grub-mkconfig -o /boot/grub/grub.cfg"
# 这样grub菜单选项才会包含该内核
pacstrap -K /mnt base linux linux-firmware
```

###### 虚拟机安装

```sh
# qemu虚拟机先安装base，如果安装失败提示先执行"pacman-key --init"命令就先执行它
pacstrap -K /mnt base

# 然后安装内核包
# linux-firmware可以不安装
# 如果安装linux包的时候提示找不到/etc/vconsole.conf，直接创建一个就行了
pacstrap -K /mnt linux
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
# 只要在/etc/locale.conf的环境变量使用到某个locale，就必须先用locale-gen生成，否则不会生效
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
systemctl enable NetworkManager

# 安装防火墙
sudo pacman -S firewalld

# 设置防火墙开机自启动
sudo systemctl enable firewalld

# 启动防火墙
sudo systemctl start firewalld
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

# 设置图形终端的分辨率
# 可以在GRUB环境中使用"videoinfo"命令查看图形卡支持的模式有哪些
# auto作为保留，当前面的模式都不能用的时候就会fallback，自动选择可用的模式
GRUB_GFXMODE=1920x1080,1600x900,1280x720,auto

# 让内核使用的分辨率和grub的一样
GRUB_GFXPAYLOAD_LINUX=keep

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

##### 安装usb_modeswitch

如果用usb网卡，遇到了插拔后usb网卡无法识别

或者关机然后断电，再通电开机，进入桌面后发现usb网卡无法识别的情况

笔者安装usb_modeswitch后usb网卡就能正常自动识别了

```sh
sudo pacman -S usb_modeswitch
```

如果不能识别，可以手动执行（可能每次开机都要执行）

```sh
# 查看usb网卡的信息如:obda:1a2b.....
lsusb

# -M 是厂商对指定型号的usb网卡从CD-ROM模式转为网卡模式的命令
sudo usb_modeswitch -v 0bda -p 1a2b -M "5553424312345678000000000000061b000000020000000000000000000000" -W
```

另一种办法是手动切换为网卡模式，参考：<https://wiki.archlinux.org/title/ZTE_MF110/MF190#Switch_from_CD_mode_to_modem_mode_on_the_device>

#### 安装中文字体

Proportional Fonts：比例字体，也就是非等宽字体，每个字符宽度不同，比如：i比m窄

Monospace：等宽，一个汉字=两个ASCII字母的宽度，全宽字符：宽度等于一个汉字的宽度，半宽字符：宽度等于半个汉字的宽度

Serif：有衬线，笔画末端有小装饰线，如宋体

Sans-serif：无衬线，笔画末端没有装饰，线条简洁

![衬线和无衬线对比图](/images/Sans-Serif.png)

##### 安装思源字体和ttf-hanazono

至于为什么不安装adobe版的思源黑体（adobe-source-han-sans-cn-fonts）和思源宋体（adobe-source-han-serif-cn-fonts）呢？

- 目前的主要原因：
    - 第一是adobe版的是分开一个一个包的，不像谷歌版的只要安装一个包就可以了，单单下载中文字体还好才三个包，要是想兼容其它语言要安装的包就多了
    - 等宽字体（adobe-source-han-mono-cn-fonts）目前不在archlinux官方包仓库，只能从aur仓库下载

```sh
# 选择一：谷歌版的思源黑体（包含了等比例/等宽字体），覆盖简体、繁体、日文、韩文（cjk分别是中日韩的英文首字母）
# 不安装中文会乱码，装一个够了，不够用再找别的字体
sudo pacman -S noto-fonts-cjk

# 选择二：adobe版的思源字体，安装中文三件套（等比例的有衬线和无衬线，等宽）就行了，占用的磁盘空间会更小
yay -S adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts adobe-source-han-mono-cn-fonts

# 包含更多的汉字特别是生僻字和繁体字（据说是unicode包含的全部汉字），根据需要安装
# 笔者建议安装，因为fcitx5的候选字就有很多是生僻字的，不安装的话会显示为相应的unicode码
# 它只有衬线字体，没有无衬线和等宽字体
sudo pacman -S ttf-hanazono

# 了解即可，建议还是重启
# 然后用来刷新和重建系统的字体缓存（只能刷新部分字体缓存，如fcitx5的候选字就不会马上生效，需要重启才行）
# fc-cache：Fontconfig
# -f (force)：强制刷新，即使缓存已经存在也会重新生成
# -v (verbose)：详细模式，会在终端输出扫描过程和结果，方便确认哪些字体被识别
fc-cache -fv
```

##### noto-fonts-cjk需额外配置字体优先级

因为noto-fonts-cjk默认是日文优先，有些汉字显示为日文很别扭，需要进行额外的配置

创建文件`~/.config/fontconfig/conf.d/99-fonts-priority.conf`

- 文件名前缀`00- ~ 99-`，系统会按照00到99的顺序读取这些文件，后读取的文件配置可能会覆盖先读取的文件配置，输入文件内容如下

- 方法1（推荐）：只修改非未指定语言或非日文下时的优先级，不会覆盖noto-fonts-cjk的其它默认配置

```xml
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'urn:fontconfig:fonts.dtd'>
<fontconfig>
    <!-- 当语言属性未指定或非日文时，优先使用简体中文字体，最后HanaMinA和HanaMinB做兜底 -->
    <!-- 为无衬线字体(sans-serif)设置默认优先级 -->
    <match target="pattern">
        <test name="family">
            <string>sans-serif</string>
        </test>
        <test name="lang" compare="not_contains">
            <string>ja</string>
        </test>
        <edit name="family" mode="prepend" binding="strong">
            <string>Noto Sans CJK SC</string>
            <string>HanaMinA</string>
            <string>HanaMinB</string>
        </edit>
    </match>

    <!-- 为有衬线字体(serif)设置默认优先级 -->
    <match target="pattern">
        <test name="family">
            <string>serif</string>
        </test>
        <test name="lang" compare="not_contains">
            <string>ja</string>
        </test>
        <edit name="family" mode="prepend" binding="strong">
            <string>Noto Serif CJK SC</string>
            <string>HanaMinA</string>
            <string>HanaMinB</string>
        </edit>
    </match>

    <!-- 为等宽字体(monospace)设置默认优先级 -->
    <match target="pattern">
        <test name="family">
            <string>monospace</string>
        </test>
        <test name="lang" compare="not_contains">
            <string>ja</string>
        </test>
        <edit name="family" mode="prepend" binding="strong">
            <string>Noto Sans Mono CJK SC</string>
            <string>HanaMinA</string>
            <string>HanaMinB</string>
        </edit>
    </match>
</fontconfig>
```

- 方法2：会覆盖noto-fonts-cjk的默认全局配置，简单粗暴，但是不推荐

```xml
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'urn:fontconfig:fonts.dtd'>
<fontconfig>
    <!-- 设置无衬线字体（大部分UI界面）的优先级 -->
    <alias>
        <family>sans-serif</family>
        <prefer>
            <family>Noto Sans CJK SC</family>
            <family>Noto Sans CJK TC</family>
            <family>HanaMinA</family>
            <family>HanaMinB</family>
        </prefer>
    </alias>

    <!-- 设置有衬线字体的优先级 -->
    <alias>
        <family>serif</family>
        <prefer>
            <family>Noto Serif CJK SC</family>
            <family>Noto Serif CJK TC</family>
            <family>HanaMinA</family>
            <family>HanaMinB</family>
        </prefer>
    </alias>

    <!-- 设置等宽字体（终端、代码编辑器）的优先级 -->
    <alias>
        <family>monospace</family>
        <prefer>
            <family>Noto Sans Mono CJK SC</family>
            <family>Noto Sans Mono CJK TC</family>
            <family>HanaMinA</family>
            <family>HanaMinB</family>
        </prefer>
    </alias>
</fontconfig>
```

- 方法3

```sh
# 再安装一个adobe版的思源黑体中文，然后在系统设置里面设置它为默认字体就行了，甚至不用设置都会优先用它显示中文了
# 简单但是浪费存储空间
sudo pacman -S adobe-source-han-sans-cn-fonts
```

##### 系统字体设置

字体大小跟系统默认一样，除了Fixed width设置为"Noto Sans Mono CJK SC"，其它都用"Noto Sans CJK SC"，

特别是General那里，一定要设置，不然有些中字会显示半宽，特别别扭

![系统字体设置](/images/system-fonts.png)

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
# 会提示jack2和pipeware-jack选一个安装，选前者
# 会提示qt6-multimedia-ffmpeg和qt6-multimedia-gstreamer选一个安装，选前者
sudo pacman -S plasma-desktop

# kde-gtk-config，官方的描述是同步KDE设置到GTK应用，人话就是让GTK应用（如Firefox）在KDE（基于QT）下的外观和行为更接近QT应用
# 必须安装kde-gtk-config，否则火狐浏览器的最大化和最小化按钮是不显示的
sudo pacman -S kde-gtk-config

# plasma-nm，管理网络连接，并且在任务栏显示网络托盘图标，可作为NetworkManager的GUI
# plasma-pa，音量控制器，并且在任务栏显示托盘小喇叭
# kscreen，显示设置，如设置分辨率、缩放等，不安装的话系统设置里面Display & Monitor选项置灰
# plasma-firewall，防火墙控制面板
sudo pacman -S plasma-nm plasma-pa kscreen plasma-firewall
```

- 只安装kde-applications的部分应用

```sh
# konsole，终端模拟器，支持标签、透明、快捷键
# dolphin，文件管理器，支持标签、网络挂载、批处理等
sudo pacman -S konsole dolphin 
```

##### 安装SDDM（图形登录和会话管理器）

SDDM：Simple Desktop Display Manager

完整的plasma只包含了sddm-kcm（sddm的kde桌面配置工具），还需要自行安装SDDM

不用单独安装xorg，安装sddm的时候会将其作为依赖自动安装

```sh
sudo pacman -S sddm

# 可选安装，可以在系统设置选择登录界面的样式
sudo pacman -S sddm-kcm

# 启用SDDM开机启动
sudo systemctl enable sddm
```

- 设置锁屏界面显示的时间为24小时制

```sh
# 查看C.UTF-8是否已经内置有了，如果没有需要先生成
locale -a | grep C

# 添加：LC_TIME=C.UTF-8，然后重启
vim /etc/locale.conf
```

##### 创建非root用户

sddm登录界面只默认显示一个非root用户给你输入密码，然后登录进入桌面，因此这里要先创建一个非root用户

```sh
# 创建非root用户和家目录，这里以handle为例
useradd -m handle

# 指定handle的登录密码
passwd handle

# 用户加入 wheel 组，然后重启，新的组权限才会生效，这里以上面创建的handle为例
# usermod 修改用户账户的命令
# -a append，追加组（不移除已有组）
# -G wheel 指定要加入的组是 wheel
usermod -aG wheel handle
```

##### （root用户）安装sudo

sudo + 命令：以系统管理者的身份执行指令，也就是说，经由 sudo 所执行的指令就好像是 root 亲自执行

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

# 或者这样（还没试过）
# -i，直接修改文件（in-place edit）
# s/旧内容/新内容/，找到旧内容，替换为新内容
# 如果旧内容有正则符号要用\转义
# 如果旧内容或新内容有/要对/转义或者写成s|旧内容|新内容|,也可以将|换成其它分隔符
# ^，匹配行首
sed -i 's/^# %wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers
```

##### 安装yay

AUR（Arch User Repository）是Arch Linux社区维护的一个用户贡献的包脚本仓库

AUR本身只提供PKGBUILD脚本，不提供二进制包

用户需要用makepkg手动下载源码、编译、打包，再用pacman安装

AUR助手（比如 yay、paru、pamac）就是帮你自动化这一整套流程的工具

yay (Yet Another Yogurt)是AUR助手之一，Yogurt：是一个早期的AUR助手

官网：<https://github.com/Jguer/yay>

- 1.下载官方编译版本.tar.gz,解压

- 2.`~/.bashrc`文件中设置环境变量

```sh
export YAY_HOME=/home/handle/Applications/yay_x86_64
export PATH=$PATH:${YAY_HOME}
```

- 3.测试yay是否安装成功

```sh
yay --version
```

- 4.安装base-devel和git

```sh
# base-devel是使用yay -S 包名 构建AUR包时用到的依赖
# 它包含了常见的编译工具和脚本，如make、gcc等
sudo pacman -S --needed base-devel

# 还要安装git
# yay → 用 git clone 从AUR下载 PKGBUILD → 编译 → 安装
sudo pacman -S git
```

- 当用yay安装AUR包很慢时
    - 用梯子
    - 用梯子还是很慢时
        - 进入构建缓存目录（存放PKGBUILD及临时构建文件）`~/.cache/yay/包名`
        - 方法1
            - 打开PKGBUILD文件，修改卡住的文件的下载地址（如果有其他的下载地址）
            - 查看修改后的下载地址对应的文件的哈希码跟PKGBUILD文件里面的哈希码是否一致，如果不一致将其替换为前者的哈希码
        - 方法2
            - 通过浏览器或其他工具下载卡住的文件，然后放到这个目录下
            - 打开PKGBUILD文件，查看手动下载好文件的哈希码跟PKGBUILD文件里面的哈希码是否一致，如果不一致将其替换为前者的哈希码
        - 重新执行安装，程序会自动跳过已下载的文件，继续构建流程

##### 安装火狐浏览器

```sh
sudo pacman -S firefox

# 启动火狐浏览器
# 进入浏览器设置，搜索font，将字体设置为"Noto Sans CJK SC"（可选）
firefox

# 解决下载莫名其妙自动暂停，点击恢复下载，立马下载失败的问题
# 地址栏输入about:config
# 搜索browser.safebrowsing.downloads.enabled，然后设置为false，立即生效
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

###### 1.安装输入法框架

```sh
# fcitx5-im是一个元包，包含了fcitx5 fcitx5-gtk fcitx5-qt fcitx5-configtool
# fcitx5，主程序
# fcitx5-gtk fcitx5-qt，UI开发工具包的输入法模块，如果装有vscode，则必须安装，否则输入法会抽风
# fcitx5-configtool，GUI配置程序
sudo pacman -S fcitx5-im
```

###### 2.安装输入法（引擎）

- 安装拼音输入法（推荐新手使用，候选词汇更多）

```sh
sudo pacman -S fcitx5-chinese-addons

# 安装适用于拼音输入法的词库，会自动加载，不用配置
sudo pacman -S fcitx5-pinyin-zhwiki
```

- 安装rime输入法

```sh
# fcitx5-rime,一种可自定义的输入法引擎，但默认情况下其默认配置为拼音
sudo pacman -S fcitx5-rime

# 安装适用于rime输入法的词库，不会自动加载，需要手动配置
# 安装后得到文件：/usr/share/rime-data/zhwiki.dict.yaml
sudo pacman -S rime-pinyin-zhwiki
```

###### 3.配置输入法

- 通用配置

```sh
# 配置环境变量
# v2rayN和微信输入法需要设置GTK_IM_MODULE、QT_IM_MODULE和SDL_IM_MODULE，虽然官方和电脑提示不需要设置，不要听
# 在~/.bashrc文件中添加如下内容
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export SDL_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx

# 然后运行输入法配置GUI，也可以通过系统设置的"Input Method"进入
fcitx5-configtool

# 然后在系统设置的"Input Method"->"Add Input Method"
# 如果安装的是拼音输入法，添加：Pinyin
# 如果安装的是rime输入法，添加：Rime
# 然后在"Input Method"，下方的"Configure addons"，选择"Classic User Interface"，然后配置字体大小，然后应用直接见效，不然候选字真的太小了
# 然后在"Input Method"，下方的"Configure addons"，选择Unicode，然后将"Type unicode in Hex number"的快捷键删除或改成别的，不然会跟idea的冲突
# 然后在系统设置KeyBoard，找到virtual keyboard，选择Fcitx 5，然后应用就可以了
```

- rime输入法需要手动配置词库

```yaml
# 进入`~/.local/share/fcitx5/rime`目录
# 在该目录下新建final.dict.yaml文件，输入如下内容
---
name: final
version: "2025.12.18"
sort: by_weight
# 是否启用默认的“八股文”词库及词频系统，如需启用请设为true 
use_preset_vocabulary: true
import_tables:
    # 这里的缩进必须用空格
    # 添加默认词库
    - luna_pinyin
    # 添加安装的rime-pinyin-zhwiki词库
    - zhwiki
...

# 继续在该目录下新建luna_pinyin.custom.yaml文件，输入如下内容
# 让指定的词库文件生效
patch:
    # final对应final.dict.yaml文件
    "translator/dictionary": final

# 然后重新部署输入法就可以了
```

###### 4.字体设置（可选）

都设置为"Noto Sans CJK SC"，字号、是否加粗用默认

![fcitx5字体设置](/images/fcitx5-fonts.png)

###### 5.创建自定义词库（可选）

因为自定义词库文件分两部分：头部是yaml格式的，用空格缩进

尾部（自定义词条）是要用到真正的制表符分隔每个词条的词汇、拼音、权重的

当用文本编辑器重新打开这个文件，可能文件头部和尾部的空格和制表符直接统一格式化为空格了，导致自定义词条不生效

因此笔者创建两个文件，一个放头部信息，一个放尾部的自定义词条

当然如果重新打开放自定义词条信息的文件还是要检查制表符有没有被编辑器替换掉，根据情况设置回制表符再保存

- 1.进入`~/.local/share/fcitx5/rime`目录(fcitx5-android目录是: Android/data/org.fcitx.fcitx5.android/files/data/rime)

- 2.在该目录下创建自定义词库文件extension.dict.yaml，存放自定义词条

```yaml
# 自定义词库
# 记得用UTF-8编码并保存
---
# extension对应extension.dict.yaml文件
name: extension
version: "2025.12.18"
sort: by_weight
...

# 以下开始为自定义词条
# 词汇、拼音、权重间用8长度的制表符，千万不要用空格，重新打开此文件也要注意检查和设置
# 权重可以不写
# \t表示制表符，例子：㘃\tre\t99%，权重99%是可以不写的
㘃        re        99%
```

- 3.在该目录下创建词库文件luna_pinyin.custom.dict.yaml
    - 也可以是"自定义名称.dict.yaml"，但是这样定义部署后会生成"自定义名称.userdb"文件夹，多余
    - 个人建议还是用"方案名称.custom.dict.yaml"

```yaml
---
name: luna_pinyin.custom
version: "2025.12.18"
sort: by_weight
# 是否启用默认的“八股文”词库及词频系统，如需启用请设为true 
use_preset_vocabulary: true
# 导入已有词库
import_tables:
    # 这里的缩进必须用空格
    # 添加默认词库
    - luna_pinyin
    # 添加安装的rime-pinyin-zhwiki词库
    - zhwiki
    # 添加自定义的扩展词库，对应自定义的extension.dict.yaml文件
    - extension
...
```

- 4.在该目录下创建luna_pinyin.custom.yaml文件（luna_pinyin为输入方案名称，根据使用的输入方案的不同要作相应变更）

```yaml
patch:
    # 让指定的词库文件生效
    # luna_pinyin.custom对应luna_pinyin.custom.dict.yaml文件
    "translator/dictionary": luna_pinyin.custom
```

- 5.然后重新部署输入法

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

###### 安装AMD显卡驱动

Linux系统的AMD显卡驱动是开源的，教程官网：<https://wiki.archlinux.org/title/Graphics_processing_unit#Installation>

```sh
# 先查看显卡信息
# ::03表示"Display controller PCI device class"
# xx 表示 "any subclass of the class"
lspci -vnnd ::03xx

# 然后根据显卡的家族，到教程官网查看表格，选择对应的驱动包进行安装
# 为了支持32位应用，先开启multilib
# mesa和lib32-mesa提供DRI驱动（应该就是OpenGL）和VA-API/VDPAU驱动，分别用于3D加速和图像/视频编解码加速
# 这两个包一般内核自带了，安装时可以先确认是否已经存在，不存在再安装
# 硬件特定的 Device Dependent X (DDX) 驱动已经过时
# xorg-server中有更通用modesetting DDX 驱动，它使用kernel mode setting并能在现代硬件上运行很好 
# modesetting DDX 驱动使用Glamor来进行2D 加速，这需要OpenGL 
# 因此很多情况下（比如说在安装了xorg环境或Wayland环境的时候）是不用安装DDX驱动的，也就是不用安装xf86-video-amdgpu 
# 通常必须安装的只有vulkan-radeon和lib32-vulkan-radeon，它们提供vulkan支持
sudo pacman -S [mesa lib32-mesa] [xf86-video-amdgpu] vulkan-radeon  lib32-vulkan-radeon 
```

###### 安装视频加速（Video acceleration），可选

mesa和lib32-mesa提供的是开源的VA-API用于视频编解码加速

此外还可以安装AMD闭源的AMF用于视频编解码加速，只需要安装amf-amdgpu-pro包就行了

```sh
# 如果想要使用AMD闭源的AMF用于视频编解码加速，需要另外安装
# 否则执行：ffmpeg -i input.mp4 -c:a copy -c:v av1_amf -quality balanced -b:v 6500k  output.mp4
# 会报DLL libamfrt64.so.1 failed to open
# [av1_amf @ 0x55d384932e80] Failed to create  hardware device context (AMF) : Unknown error occurred
# 使用ffmpeg，同参数下，如
# ffmpeg -vaapi_device /dev/dri/renderD128 -i input.mp4 -vf 'format=nv12,hwupload' -c:a copy -c:v av1_vaapi -b:v 6500k output.mp4
# ffmpeg -i input.mp4 -c:a copy -c:v av1_amf -quality balanced -b:v 6500k output.mp4
# 虽然VA-API的命令更长，但是VA-API比AMF输出视频的速度更快，视频文件更小
yay -S amf-amdgpu-pro

# libva-utils包的vainfo命令可以查看VA-API信息，如下面的AV1格式的信息
# VAProfileAV1Profile0            : VAEntrypointVLD
# VAProfileAV1Profile0            : VAEntrypointEncSlice
# VAEntrypointVLD 意味着显卡支持该格式的解码
# VAEntrypointEncSlice 意味着显卡支持该格式的编码 
sudo pacman -S libva-utils

# 安装vulkan-tools（kinfocenter和lact包都依赖它），
# 用"vulkaninfo | grep VK_KHR_video_"命令确认视频处理扩展可用，如
# VK_KHR_video_decode_av1                       : extension revision 1
sudo pacman -S vulkan-tools
```

###### 安装ROCm（可选）

如果在本地部署AI模型，还可以安装ROCm来充分使用GPU性能

```sh
# rocm-hip-sdk：Develop applications using HIP and libraries for AMD platforms
# rocm-opencl-sdk还不太懂，先装着
# 如果使用ollama，需要安装ollama-rocm才能使用GPU加速
# 像python-pytorch-opt-rocm应该在具体的环境中安装
sudo pacman -S rocm-hip-sdk rocm-opencl-sdk ollama-rocm
```

###### 安装英伟达显卡驱动

```sh
# 这一步可能还要摸索，笔者装了英特尔和英伟达的后，重启黑屏了，然后切换tty又装了optimus-manager，然后重启又正常了

# 安装 GPU 驱动（根据显卡选择，虚拟机可以不用执行）

# 根据执行结果去网站找到对应的显卡代码：https://nouveau.freedesktop.org/CodeNames.html
# 到安装教程官网，根据显卡代码安装对应的显卡驱动：https://wiki.archlinux.org/title/NVIDIA
lspci -k -d ::03xx

# Nouveau（NVIDIA开源驱动），目前英伟达没有完全开源，性能不如英伟达闭源驱动的一半，不建议安装
# linux内核包已经包含了nouveau开源驱动（即mesa和lib32-mesa不用手动安装了，provides the DRI driver for 3D acceleration）
# Mesa NVK Vulkan Driver
sudo pacman -S vulkan-nouveau lib32-vulkan-nouveau

# NVIDIA（闭源）
# 新版显卡的驱动已经从nvidia改成nvidia-open了
# nvidia-open：NVIDIA内核模块
# nvidia-utils：NVIDIA驱动工具，安装nvidia-open时会将nvidia-utils作为依赖进行安装
# lib32-nvidia-utils：NVIDIA驱动工具（32位）（可选）,Steam需要用到，笔者建议安装具体的软件的时候再根据交互提示选择安装
# nvidia-settings：NVIDIA图形驱动程序配置工具（可选）
sudo pacman -S nvidia-open lib32-nvidia-utils nvidia-settings

# 对于使用Wayland的情况，如Plasma(Wayland)，还需要进行DRM (Direct Rendering Manager) 内核模式设置
# 从nvidia-utils 560.35.03-5起,默认已经启用DRM
# 对于老版本的驱动，设置modeset=1
# 可以执行如下命令确认，输出应该为Y
sudo cat /sys/module/nvidia_drm/parameters/modeset
```

###### 安装GTX 1060显卡驱动

GTX 1060显卡驱动已经被老黄归为Legacy, supported，需要自己编译安装了，安装完重启即可

```sh
# 安装headers
# 如果是linux包就安装linux-headers
# 如果是linux-lts包安装linux-lts-headers（每个内核包都有不同的headers包名）
sudo pacman -S linux-headers

# 安装显卡驱动，如果内核有更新了，header也会更新，这时候显卡驱动也得更新了
yay -S nvidia-580xx-dkms

# 使dkms命令生效
# source /usr/share/bash-completion/completions/dkms

# 将可以看到刚刚安装的显卡驱动
# dkms status

# 对于使用Wayland的情况，如Plasma(Wayland)，还需要进行DRM (Direct Rendering Manager) 内核模式设置
# 从nvidia-utils 560.35.03-5起,默认已经启用DRM
# 对于老版本的驱动，设置modeset=1
# 先确认，输出应该为Y
# sudo cat /sys/module/nvidia_drm/parameters/modeset
```

###### 安装RTX 3050显卡驱动

RTX 3050 可以安装两种驱动：nvidia-open或者nvidia-580xx-dkms，推荐后者

因为arch维基说前者在某些情况下会崩溃

- 方法一

```sh
# linux内核安装
yay -S nvidia-open

# linux-lts内核安装
# 笔者安装linux-lts和linux-lts-headers后，
# 首次启动没问题，重启后电脑会卡在loading linux linux-lts...界面
# linux和linux-headers不会有这种情况，但是也不推荐使用
yay -S nvidia-open-lts
```

- 方法二：推荐

```sh
yay -S nvidia-580xx-dkms
```

###### 笔记本有独显和集显的还要额外安装工具指定使用哪个显卡

- 方法一：安装nvidia-prime，它依赖nvidia-utils，因此安装nvidia-open驱动的显卡才支持

```sh
# 安装nvidia-prime
yay -S nvidia-prime

# 用独显启动指定程序
prime-run 程序名
```

- 方法二：通用方式（推荐）

```sh
yay -S optimus-manager-git

# 设置开机启动
systemctl enable optimus-manager

# 启动
systemctl start optimus-manager

# 设置显卡模式：nvidia，integrated，hybrid（相当于windows系统那种集显独显根据应用来确定的模式）
optimus-manager --switch hybrid
```

###### 安装英特尔显卡驱动

Linux系统的Intel显卡驱动是开源的，教程官网：<https://wiki.archlinux.org/title/Intel_graphics>

```sh
# mesa和lib32-mesa提供OpenGL支持
# 这两个包一般内核自带了，安装时可以先确认是否已经存在，不存在再安装
# libva-intel-driver 视频加速（VA-API）
# vulkan-intel Vulkan 支持（如游戏、图形加速）
sudo pacman -S [mesa lib32-mesa] libva-intel-driver vulkan-intel
```

###### 安装Linux显卡配置工具

lact：Linux显卡配置工具，英伟达显卡、amd显卡、英特尔显卡都可以配置

官网：<https://github.com/ilya-zlobintsev/LACT>

```sh
# 安装lact
sudo pacman -S lact

# 设置lactd服务开机启动并立刻启动
sudo systemctl enable --now lactd
```

##### 安装其它常用软件

```sh
# 压缩/解压软件ark
sudo pacman -S ark

# ark不支持7z和zip解压，还需要安装7zip才能用ark解压
# 首次安装无需配置ark
# 对于右键菜单ark不支持的格式，需要7zip命令进行压缩/解压
sudo pacman -S 7zip

# ark不支持rar解压，还需要安装unrar才能用ark解压
# 首次安装无需配置ark
sudo pacman -S unrar

# 图片查看器 
sudo pacman -S gwenview

# 草莓音乐播放器
# gst-libav：默认不支持ape格式的音乐播放，装了这个就可以了
sudo pacman -S strawberry gst-libav

# haruna视频播放器：相当于给mpv加了皮肤，可以自定义设置和快捷键等
sudo pacman -S haruna

# vlc视频播放器
# vlc-plugin-ffmpeg：ffempeg插件，装了就可以播放大多数格式的视频了（只装vlc播放H.264的mp4格式视频是没有画面的，会提示相关错误）
sudo pacman -S vlc vlc-plugin-ffmpeg

# 录屏软件
sudo pacman -S obs-studio

# 画图软件
sudo pacman -S kolourpaint

# 图像处理软件
sudo pacman -S gimp

# 文本比对软件
sudo pacman -S meld

# 系统安全扫描审计工具
sudo pacman -S lynis

# 恶意软件扫描，clamav的GUI
sudo pacman -S clamtk

# 应用沙盒
sudo pacman -S firejail

# archlinux版的任务管理器，依赖nvtop，用来监控CPU、GPU、内存、网络等
# 但是mission-center无法获取到部分的进程id，但是不妨碍它可以停止应用的运行
# plasma-systemmonitor无法监测英伟达显卡的GPU使用情况，该列信息为空白，mission-center完全可以替换之
sudo pacman -S mission-center

# 安装后可以在系统设置->"About this System"查看电脑系统概要/详细信息
sudo pacman -S kinfocenter

# archlinux版的cpu-z，也可以查看系统和电脑硬件信息
sudo pacman -S cpu-x
```

##### 安装steam

steam游戏存档位置：`~/.local/share/Steam/userdata/[SteamID]/[AppID]/remote/`

其中SteamID是你的steam帐号，AppID可以在游戏库里点商店，然后在URL地址找到

```sh
# 需要启用multilib仓库
# 安装过程中会提示让你选择为lib32-vulkan-driver选择依赖
# nvidia-open选择lib32-nvidia-utils
# nvidia-580xx-dkms选择lib32-vulkan-swrast（不要选lib32-vulkan-nouveau，性能比lib32-vulkan-swrast还差，游戏游戏甚至启动不了）
# 对于amd显卡，安装驱动的时候安装了lib32-vulkan-radeon，因此不会弹出选择依赖选项
sudo pacman -S steam

# 安装完后首次用命令启动，不然可能弹不出界面，都不知道什么问题
steam
```

#### 安装wine

- 1.安装

```sh
# 需要启用multilib仓库来兼容32位的软件运行
# 安装wine-mono时会安装wine
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
wine /path/to/exe文件

# 卸载windows程序
wine uninstaller

# wine配置
winecfg
```

#### 安装protonhax

protonhax是一个运行游戏修改器的工具，作用于steam的proton兼容层下运行的游戏

官网：<https://github.com/jcnils/protonhax>

##### 安装方式1

protonhax是一个bash脚本，可以直接下载这个文件

然后添加到$PATH，如`$HOME/.local/bin/protonhax`

最后赋予执行权限`chmod 755 protonhax`就可以使用了

##### 安装方式2

```sh
yay -S protonhax
```

##### 使用

- 在steam游戏属性，运行选项里面添加`protonhax init %COMMAND%`，然后运行游戏

- 打开终端，执行`protonhax ls`查看游戏id

- 启动修改器`protonhax run 游戏id /path/to/修改器.exe`

##### 卸载

如果`修改器.exe`是一个安装包，`protonhax run 游戏id /path/to/修改器.exe`执行了安装步骤

不需要这个修改器了可以通过下面的命令卸载该修改器

```sh
# 使用指定版本的Proton自带的wine，打开指定的虚拟Windows系统的“添加/删除程序”卸载器
WINEPREFIX="/path/to/SteamLibrary/steamapps/compatdata/游戏id/pfx" "/path/to/SteamLibrary/steamapps/common/Proton - Experimental/files/bin/wine" uninstaller
```

#### 安装qemu和virt-manager

教程：<https://wiki.archlinux.org/title/QEMU>

qemu还能运行virtualbox的vdi文件，但是长期来看用qemu-img转成raw或qcow2再运行性能更好

```sh
# qemu-base：命令行版本
# qemu-desktop：只有x86_64架构
# qemu-full：完全版，还包含其它架构
sudo pacman -S qemu-desktop

# libvirt通过命令行管理KVM/QEMU（虚拟机）
# virt-manager是通过libvirt管理KVM/QEMU（虚拟机）的GUI
# virt-manager->libvirt-glib->libvirt
# 安装了virt-manager不用单独安装libvirt
sudo pacman -S virt-manager

# win11要求TPM2.0，需要安装swtpm进行模拟
sudo pacman -S swtpm

# 轻量级，易于配置DNS转发器和DHCP服务器的一个工具
# 作为默认的NAT/DHCP，不安装启动NAT网络的时候会报错
sudo pacman -S dnsmasq

# openbsd-netcat是一个极简但强大的网络调试与数据转发工具，
# 能建立 TCP/UDP 连接、监听端口、做端口转发、做管道式数据传输，是 SSH 的辅助工具
# 但是笔者目前没用到...
sudo pacman -S openbsd-netcat 

# 设置身份验证
# 如果Arch Linux用户已经加入到wheel用户组了就可以跳过，会在启动virt-manager的时候提示输入密码
# 如果没有加入wheel用户组，则将用户加入wheel组或libvirt组就行了
# 如果加入libvirt组了，在启动virt-manager的时候可以不用输入密码，还是推荐加入吧
usermod -aG libvirt 用户名
sudo usermod -aG libvirt $USER && newgrp libvirt

# 设置开机自启动，然后重启
sudo systemctl enable libvirtd
```

##### virt-manager设置

- Edit->Preferences
    - General
        - 勾选“Enable XML editing”
        - 勾选“Enable libguestfs VM introspection”

    - New VM->x86 Firmware->如果你新建虚拟机想要用UEFI就设置为UEFI，不设置会根据系统镜像默认

```sh
# 勾选“Enable libguestfs VM introspection”，提示安装libguestfs就照做，然后重启virt-manager
sudo pacman -S libguestfs
```

##### 配置共享剪切板

- Arch Linux

```sh
# 在虚拟系统里面安装(archlinux)
sudo pacman -S spice-vdagent

# 设置开机自启动，然后重启
sudo systemctl enable spice-vdagentd
```

##### 配置共享目录

- 选中虚拟机->Edit->Virtual Machine Details
    - Memory->勾选Enable shared memory
    - Add Hardware->Filesystem->配置

##### 复制虚拟机

原虚拟机先关机，复制后可以在virt-manager直接看到新虚拟机，无需通过添加虚拟机步骤添加了，挺方便的

复制后，如果原虚拟机和新虚拟机要同时启动，记得修改新虚拟机的hostname

```sh
# --file：指定新虚拟机文件的位置，所在目录需要先创建，如果不指定，将在原虚拟机文件的目录新建一个newDomain.qcow2
# --check disk_size=off：不检测存放新虚拟机文件的磁盘的大小，如果不加这个参数，当磁盘文件小于原虚拟机文件预定义大小时会终止复制
sudo virt-clone --original oldDomainName --name newDomainName --file /path/to/newDomain.qcow2 --auto-clone --check disk_size=off
```

##### 复制虚拟机文件

- 不能使用gui的常规复制粘贴，不然它会按预定义大小来执行复制

- 目前没有快照的情况下用rsync命令复制ubuntu，然后启动是没有问题的

- 目前没有快照的情况下用qemu-img命令复制ubuntu，然后启动也是没有问题的

- 更推荐qemu-img，它复制后的文件用文件管理器查看占用的空间变成了实际占用空间而不是预定义空间大小

- 目前有外部快照用rsync命令复制后，新增虚拟机，快照丢失了，这种情况，要考虑复制的话继续用virtualbox比较好
    - ubuntu复制启动没有问题，但是系统状态为创建快照那时候的系统
    - archlinux启动引导丢失，修复后，系统状态为创建第一个快照那时候的系统

```sh
# archlinux安装rsync 
sudo pacman -S rsync

# rsync适合复制有外部快照文件的虚拟机，没有外部快照文件的情况更推荐用qemu-img转换命令
# rsync只能复制，不能整理qcow2碎片
# -a：归档
# -h：人类可读
# --sparse：保留稀疏文件结构，不会把qcow2/raw膨胀成预定义大小，对于普通文件如果加了这个参数rsync不会做任何额外处理
# --info=progress2：复制总体进度
# 要先创建目的目录，最后原目录记得加/后缀，表示复制目录内容
sudo mkdir -p /path/to/target
sudo rsync -ah --sparse --info=progress2 /path/to/vmdirectory/ /path/to/target

# qemu-img适合转换没有外部快照文件的虚拟机
# 如果有外部快照，则效果是相当于将虚拟机文件合并，最后只有一个虚拟机文件并且不会有backing file了
# qemu-img在转换完成后生成的目标文件大小将不再是转换前原文件那样显示的是预定义大小
# 而是显示为实际占用空间的大小，预定义大小不变
# -p：显示进度
# -O：目标格式，默认raw
sudo qemu-img convert -p -O qcow2 /path/to/active.qcow2 /path/to/target.qcow2

# 如果有外部快照，假设虚拟机文件的生成顺序是：domain.qcow2 -> domain.snapshotname1 -> domain.snapshotname2
# 转换后的目标文件内容为：domain.qcow2
# 用这个命令可以将domain.qcow2文件转成显示为实际大小而不是预定义大小的目标文件
# 此外如果domain.snapshotname1和domain.snapshotname2还是用rsync复制的话，跟rsync复制整个虚拟机文件夹的效果是差不多的
sudo qemu-img convert -p -O qcow2 /path/to/domain.qcow2 /path/to/target.qcow2

# 转换后的目标文件内容为：domain.qcow2 + domain.snapshotname1
sudo qemu-img convert -p -O qcow2 /path/to/domain.snapshotname1 /path/to/target.qcow2

# 转换后的目标文件内容为：domain.qcow2 + domain.snapshotname1 + domain.snapshotname2
sudo qemu-img convert -p -O qcow2 /path/to/domain.snapshotname2 /path/to/target.qcow2

# 查看qcow2文件预定义大小和实际占用空间大小
sudo qemu-img info ubuntuserver.qcow2

# 必须复制原虚拟机的，然后在新建虚拟机前
# 将其复制到/var/lib/libvirt/qemu/nvram/新虚拟机名称_VARS.fd
# 不然新建虚拟机时，virt-manager生成的是一个错误的，导致启动引导失败（想骂人！）
# failed to load Boot0002 "UEFI Misc Device" from t...: Not found
sudo cp /var/lib/libvirt/qemu/nvram/虚拟机名称_VARS.fd /path/to/target

# 如果有快照，还要复制快照文件
# 如果是ArchLinux，提示必须用新虚拟机的uuid，就将<domain>...</domain>里面的name和uuid改成：
# <name>新虚拟机名称</name>和<uuid>新虚拟机uuid</uuid>，然后就可以导入了
# 如果是Ubuntu，导入提示必须用原来虚拟机的uuid，就删除所有快照文件里的<domain>...</domain>整段
# （想骂人！）
sudo cp -r /var/lib/libvirt/qemu/snapshot/虚拟机名称 /path/to/target

# 如果虚拟机使用了TMP，没试过先保留
#cp -r /var/lib/libvirt/swtpm/虚拟机名称 /path/to/target
```

##### 从虚拟机文件创建新虚拟机并恢复快照

###### 新建虚拟机

- 1.将"原虚拟机名称_VARS.fd"复制到"/var/lib/libvirt/qemu/nvram/新虚拟机名称_VARS.fd"

```sh
sudo cp /path/to/原虚拟机名称_VARS.fd /var/lib/libvirt/qemu/nvram/新虚拟机名称_VARS.fd
```

- 2.新建虚拟机
    - 如果"原虚拟机名称_VARS.fd"丢失了，新建虚拟机时，virt-manager将自己生成一个"/var/lib/libvirt/qemu/nvram/新虚拟机名称_VARS.fd"
    - 这很可能导致导致启动引导失败（archlinux，ubuntu目前试了没问题）
    - 如果新建虚拟机启动EFI引导失败，这时候也可以手动修复引导
        - 首先需要先删除该虚拟机，然后再次新建虚拟机
        - 在新建虚拟机最后一步勾选"Customize configuration before install"，然后点下一步
        - 在Overview->Hypervisor Details->Firmware，选择具体EFI，根据需要选是否带secureboot的，archlinux不需要带secureboot的
        - 然后开始安装，将看到启动报错：failed to load Boot0002 "UEFI Misc Device" from t...: Not found
        - 按下任意键，然后选择 EFI Internal Shell（如果上面没有选择具体EFI是没有这个选项的）
        - 然后

```sh
# 进入EFI系统分区（ESP）
FS0:

# 查看目录，能看到EFI目录
ls

# 进入EFI目录
cd EFI

# 查看目录，能看到GRUB
ls

# 进入GRUB目录
cd GRUB

# 查看目录，能看到grubx64.efi
ls

# 永久修复启动项，也可以直接执行这条指令，通过tab键补全确认是否有FS0:\EFI\GRUB\grubx64.efi
bcfg boot add 0 FS0:\EFI\GRUB\grubx64.efi "GRUB"

# 然后重启系统
reset
```

###### 恢复backing file（如果有外部快照文件）

- 恢复backing file

```sh
# 对于有外部快照的虚拟机文件
# 由于backing file的路径是写死在虚拟机文件里面的，因此复制后要重新rebase
# 假设虚拟机文件顺序为domain.qcow2 -> domain.snapname1 -> domain.snapname2
# 先进入虚拟机文件所在目录，如果不进入虚拟机文件所在目录则要写全路径
# 然后依次执行
# 这样执行完后，就恢复了
# 如果不执行rebase，则domain.snapname1和domain.snapname2根本不能使用
sudo qemu-img rebase -u -F qcow2 -b domain.qcow2 domain.snapname1
sudo qemu-img rebase -u -F qcow2 -b domain.snapname1 domain.snapname2
```

- 指定虚拟机文件

```sh
# 创建新虚拟机后，如果有正确的backing file的外部快照文件
# 可以指定快照文件名来设置虚拟机的系统状态，相当于恢复到指定快照的系统状态
# 这对于还没有恢复快照的情况下是很有用的
# 通过virt-manager的查看虚拟机详情-概览，切到xml，找到<source file="/path/to/虚拟机名称.suffix"/>
<source file="/path/to/你要指定的虚拟机文件名"/>
```

###### 恢复外部快照（恢复后在virt-manager的快照列表可以看到）

- 如果有快照名.xml文件
    - 将快照名.xml文件<domain>...</domain>块的内容替换为新虚拟机的
    - 直接复制新虚拟机的xml（virt-manager的虚拟机详情-概览的xml就是）

- 如果没有备份快照名.xml文件，也可以手动生成

```xml
<domainsnapshot>
  <!-- 填写快照名称 -->
  <name>domain.shapshotname</name>
  <state>shutoff</state>
  <!-- 假设虚拟文件生成顺序为：domain.qcow2 -> domain.shapshotname1 -> domain.shapshotname2 -->
  <!-- 则如果当前定义的快照文件是shapshotname1.xml，则不用写parent块，shapshotname2开始要写parent块 -->
  <!-- shapshotname2的parent块里的name填shapshotname1，以此类推 -->
  <parent>
    <name>finished-system-setup</name>
  </parent>
  <!-- 暂时保留，不知道是不是必须写 -->
  <creationTime>1767704084</creationTime>
  <memory snapshot='no'/>
  <disks>
    <disk name='vda' snapshot='external' type='file'>
      <driver type='qcow2'/>
      <!-- 根据快照文件的实际路径填写 -->
      <source file='/path/to/domain.snapshotname'/>
    </disk>
  </disks>
  <domain>
    <!-- 这里的内容直接复制新虚拟机的xml，virt-manager的虚拟机详情-概览的xml就是 -->
  </domain>
</domainsnapshot>
```

- 导入快照(创建新虚拟机并且虚拟系统首次关机后)

```sh
# 假设快照文件顺序为：快照1->快照2
virsh snapshot-create [--redefine] [--current] --domain 虚拟机名称 快照1.xml
virsh snapshot-create [--redefine] [--current] --domain 虚拟机名称 快照2.xml
```

##### 扩容

raw和qcow2都支持扩容

```sh
# 如果有外部快照，需要按顺序扩容
qemu-img resize base.qcow2 256G
qemu-img resize snap1.qcow2 256G
qemu-img resize snap2.qcow2 256G
qemu-img resize active.qcow2 256G
```

##### Libvirt

Libvirt 是提供了一种便捷方式来管理虚拟机和虚拟化功能的软件集合，例如存储和网络接口管理

它包括一个长期稳定的C API、一个守护线程（libvirtd）和一个命令行工具（virsh）

而我们安装的virt-manager就相当于Libvirt的GUI

使用virsh命令需要root权限

###### 存储池（Storage pools）

可以简单理解为存放虚拟机镜像的一个目录

- 操作存储池

```sh
# 创建存储池
sudo virsh pool-define-as poolname dir

# 启动存储池
sudo virsh pool-start     poolname
sudo virsh pool-autostart poolname

# 删除存储池
sudo virsh pool-undefine  poolname

# 列出所有存储池
sudo virsh pool-list --all
```

- 操作卷（虚拟机镜像）

```sh
# 创建指定格式和大小的卷
sudo virsh vol-create-as poolname volumename 128GiB --format aw|bochs|raw|qcow|qcow2|vmdk

# 把volumepath指向的文件内容写入volumename卷中，覆盖卷的内容
sudo virsh vol-upload  --pool poolname volumename volumepath

# 调整卷大小
sudo virsh vol-resize  --pool poolname volumename 12GiB

# 删除卷
sudo virsh vol-delete  --pool poolname volumename

# 列出存储池里面的卷
sudo virsh vol-list poolname

# 查看卷的详细信息
sudo virsh vol-dumpxml --pool poolname volumename
```

###### 域（Domains）

域即虚拟机

```sh
# 新建虚拟机
# --vcpus=2,maxvcpus=4：虚拟机启动时使用2个vCPU，允许未来热插拔扩展到最多4个
# --cpu host：把宿主机CPU的全部特性暴露给虚拟机，让虚拟机使用宿主机的真实CPU指令集
# --network user：使用qemu user-mode NAT
# --network network=mynetName,model=virtio：使用自己创建的网络，并且用virtio模型（性能最好）
# --virt-type kvm：使用KVM加速
sudo virt-install  \
  --name 虚拟机名称 \
  --memory 8192             \
  --vcpus=2,maxvcpus=4      \
  --cpu host                \
  --cdrom /path/to/arch-linux_install.iso \
  --disk pool=存储池名称,size=128,format=qcow2  \
  --network user            \
  --virt-type kvm

# 导入已存在的卷
sudo virt-install  \
  --name 虚拟机名称  \
  --memory 8192 \
  --disk /path/to/mydisk.qcow2 \
  --import

# [只]显示虚拟机名称
sudo virsh list --all [--name]
```

- 虚拟机操作

```sh
# 启动虚拟机
sudo virsh start domain

# 强制关机
sudo virsh destroy  domain

# 编辑虚拟机定义文件(XML)
sudo virsh edit domain

# 创建快照
# --disk-only：只对磁盘做快照，不保存内存状态
# --atomic：要么所有磁盘快照都成功，要么全部失败
sudo virsh snapshot-create-as domain snapshotName --disk-only --atomic
```

###### 快照操作

```sh
# 将 domain.snapshot1的数据合并到上一层（domain.qcow2)
# 然后就可以删除domain.snapshot1和snapshot1.xml了
sudo virsh blockpull --domain domain --path /vms/domain.snapshot1

# 列出虚拟机的所有快照
sudo virsh snapshot-list domain

# 查看快照链
sudo qemu-img info --backing-chain 虚拟机名称.快照名称 [| grep "backing file:"]
```

###### libvirt网络

libvirt的NAT网络不需要像virtualbox那样设置端口映射才能访问，默认就能访问

```sh
# 列出网络
sudo virsh -c qemu:///system net-list --all

# dump网络信息，只查看
sudo virsh -c qemu:///system net-dumpxml 网络名
```

- 给虚拟机设置静态IP

```sh
# 编辑网络（要先关停网络）
# "virsh net-edit 网络名"默认用vi打开，这里强制使用vim
sudo EDITOR=vim  virsh net-edit 网络名

# 在dhcp块中设置静态ip
<dhcp>
    # ...，在<range />后面写就行了
    <host mac="虚拟机mac" ip="你要定义的静态ip"/>
    # 路由是192.168.122.1，因此ip从192.168.122.2开始
    # 当定义192.168.122.2-192.168.122.100为静态ip时，直接指定就行了
    # start不需要设置为192.168.122.100
    <range start="192.168.122.2" end="192.168.122.254"/>
</dhcp>
```

###### 其它操作

```sh
# 列出system模式的虚拟机
sudo virsh --connect qemu:///system list --all

# 用virt-manager通过ssh连接远程虚拟机（系统模式），打开虚拟机的图形界面
sudo virt-manager --connect qemu+ssh://username@host/system domain
```

##### KVM

官网：<https://wiki.archlinux.org/title/KVM>

KVM：Kernel-based Virtual Machine，包含在linux内核里面

```sh
# 检查处理器是否支持KVM，如英特尔处理器会打印：VT-x
LC_ALL=C.UTF-8 lscpu | grep Virtualization

# Arch Linux内核提供了用于支持KVM的内核模块，输出应该是：y或m
zgrep CONFIG_KVM= /proc/config.gz

# 检查kvm内核模块是否自动加载
lsmod | grep kvm

# Virtio API半虚拟化
# 在虚拟机中输入命令检查是否支持，以及是否自动加载内核模块了
zgrep VIRTIO /proc/config.gz
lsmod | grep virtio

# 检查是否开启了嵌套虚拟化
# 如果使用virt-manager，还要到虚拟机详情里面的CPU设置那里设置host-passthrough
cat /sys/module/kvm_intel/parameters/nested
```

#### 安装VirtualBox

十分不推荐自己安装.run的软件包

```sh
# 然后根据提示选择
# 如果用的是linux包，就选择virtualbox-host-modules-arch
# 这个包是专门为Arch官方的默认内核linux编译好的VirtualBox内核模块
# 安装它后无需自己编译，也不需要额外安装linux-headers
# 如果用的是非linux包（如linux-lts），就选择virtualbox-guest-dkms
# 并且还要安装linux-lts-headers（每个内核包都有不同的headers包名）
# 安装完后如果运行虚拟系统出问题先重启看看
sudo pacman -S virtualbox

# 如果还需要虚拟机增强功能（共享剪贴板、共享文件夹等）
# 可以在虚拟机系统里面安装（archlinux）
sudo pacman -S virtualbox-guest-utils

# 然后启动服务
sudo systemctl start vboxservice

# 设置开机自启动
sudo systemctl enable vboxservice
```

##### 安装win11

勾选efi，首次启动提示按任意键选择CD/DVD的时候随便按一个键就行，千万不要再弹窗的时候重新选择系统镜像，会陷入循环的

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
sudo debtap /path/to/file.deb

# 安装转换后的包
sudo pacman -U /path/to/file.pkg.tar.zst
```

#### 安装f3

f3：Fight Flash Fraud, or Fight Fake Flash，是一个U盘真实容量检测工具

官网：<https://github.com/AltraMayor/f3>

##### 安装命令行版本的f3

```sh
yay -S f3
```

- 检测/修复
    - 方法1：f3write/f3read，
        - 只能告诉你“能写多少”，不能告诉你“真实容量边界”，无法用于修复，并且速度慢
        - 一般用于文件系统层面的压力测试，看看平均读写速度
    - 方法2：f3probe，能给出真实容量的精确扇区号last-sec，用于修复

```sh
# 1.向U盘写入大量测试文件，用来填满整个盘
f3write /path/to/你的U盘挂载点

# 2.读取刚才写入的文件，检查是否有损坏或循环覆盖
f3read /path/to/你的U盘挂载点

# 先查看U盘的设备名称
sudo fdisk -l

# 快速测试，要先卸载U盘
# 如果是缩水U盘，将会打印类似：
# f3fix --last-sec=8034287 /dev/sdc
# *Usable* size: 3.83 GB (8034288 blocks)
# 表示这个U盘可用的扇区大小为8034288（0-8034287，最后一个扇区是8034287）
# 每个扇区512字节，可用容量：8034288*512/1024/1024/1024=3.83GB
sudo f3probe --destructive --time-ops /dev/sdX

# 将U盘改回真实容量
# f3fix命令会创建MBR分区表、创建一个FAT32分区和格式化分区
# 也可以用其他分区工具进行设置
# 要先卸载U盘
# 需要注意的是，如果用缩水U盘制作系统启动盘，那么U盘原有的分区表/分区是会被覆盖掉的
# 制作完启动盘后，系统显示该U盘的容量依然会变回假容量
# 除非重新刷U盘控制器固件，前提是必须知道控制器型号（VID/PID）、找到对应的量产工具、找到正确的固件配置
# 假U盘通常用山寨控制器，资料极少，刷错一次U盘直接变砖
# 因此笔者建议还是用工具分区为可用大小的方式
# 谨慎将缩水U盘制作为启动盘，至少要考虑到启动盘需要的空间和缩水的U盘的可用空间这一点再作下一步决定
f3fix --last-sec=16477878 /dev/sdX
```

##### 安装GUI版本的f3

```sh
# 安装f3-qt将自动安装作为依赖的f3
yay -S f3-qt
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
# 指定桌面快捷方式图标下方显示的默认名称
Name=WeChat
Exec=/home/handle/Applications/WeChat.AppImage
Icon=/home/handle/Applications/WeChat.png
Terminal=false
Categories=Development;
# 这个应用是否支持启动通知：应用启动时显示“正在启动”的动画，在任务栏显示一个“启动中”的高亮，等应用真正启动后再恢复正常
# 如果应用支持这个机制，桌面环境就能知道它什么时候启动完成。
StartupNotify=true
```

- 如果是wine，则Exec键值对需要做变动

```sh
Exec=wine /path/to/App.exe
```

### 获取下载文件的哈希码

```sh
# 根据官方给的哈希码执行对应的命令
sha256sum /path/to/file

sha512sum /path/to/file
```

### 系统启动美化

需要在系统设置plymouth-kcm那里另外下载主题，默认的主题显示不出来

试了一下就关机/重启的时候看起来比较有感觉

但是开机的时候，显示两行初始化的日志文本，到显示开机动画期间，会看到花屏，要么就是根本没显示开机动画，体验感很差，不建议安装

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
# HOOKS=(...systemd...plymouth...encrypt sd-encrypt...)
# 如果用的是systemd，则plymouth要放在其后面
# 如果系统用dm-crypt加密，则plymouth要放在encrypt or sd-encrypt之前
sudo vim /etc/mkinitcpio.conf

# 重新生成 initramfs
sudo mkinitcpio -P
```

### 目录/文件备份

整个系统的备份笔者认为是没有必要的，下面列出几个重要的目录/文件，当系统挂掉可以通过LiveCD进入系统复制出来

```sh
# （AppImage、tar.gz等免安装）应用放这里了，方便复制
~/Applications

# 应用配置
~/.config

# 桌面快捷方式文件
~/.local/share/applications

# 桌面快捷方式文件或链接文件
~/Desktop

# 环境变量
~/.bash_profile
~/.bashrc
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

# 或用cfdisk（如果有），图形化的fdisk 
cfdisk /dev/the_disk_to_be_partitioned

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

- 创建（格式化）文件系统（分区）

```sh
# 列出设备和分区信息
fdisk -l

# 创建一个新的文件系统（即格式化为指定的文件系统），前提是该分区必须已经卸载
# 不同文件系统对应不同的命令，以分区/dev/sda1为例
# -L，标签
mkfs.ext4 -L os /dev/sda1
mkfs.exfat /dev/sda1
mkfs.fat -F 32 /dev/sda1
```

- 挂载/卸载文件系统（分区）

```sh
# 挂载一个文件系统，以分区/dev/sda1，挂载点/mnt/data为例
# 这种挂载方式重启后挂载会失效，要重新挂载
# 想要永久挂载需要修改/etc/fstab，修改完后执行"mount -a"挂载立即生效
# --mkdir：当还没创建/mnt/data目录时，自动创建该目录
mount --mkdir /dev/sda1 /mnt/data

# 如果给分区设置了label
# 在/dev/disk/by-label目录下就会出现一个和labelName同名的符号链接
# 可以用下面的命令进行挂载
mount --mkdir /dev/disk/by-label/labelName /mnt/data


# 卸载一个文件系统，以分区/dev/sda1，挂载点/mnt/data为例
# 方法1：通过指定分区名称卸载
umount /dev/sda1

# 方法2：通过指定挂载点卸载
umount /mnt/data

# 方法3：通过分区label卸载
umount /dev/disk/by-label/labelName

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

- 给分区设置label，不会导致数据被格式化

```sh
# 对于ext4的分区，下面两个命令都可以
e2label /dev/partition_name labelName
tune2fs -L labelName /dev/partition_name
```

### uv的使用

包管理器：uv pip

```sh
# archlinux安装uv
sudo pacman -S uv

# 创建一个新项目（python环境）
uv init

# 创建一个环境，比如在项目中删除.venv文件夹后可以执行，这样就重新创建一个环境了
# --python 3.9：指定python版本
uv venv [--python 3.9]

# 在新项目中添加依赖
uv add

# 在新项目中删除依赖
uv remove   

# 更新项目环境
# 指定国内源
uv sync --default-index "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"

# 清空缓存
uv cache clean
```

### miniforge的使用

miniforge可以看成是增强版的miniconda，跟uv一样把它当成包管理器就行了

它用的是mamba命令，conda命令执行的语句都可以将conda替换为mamba

```sh
# 安装miniforge
yay -S miniforge

# 安装完成为当前用户执行
echo "[ -f /opt/miniforge/etc/profile.d/conda.sh ] && source /opt/miniforge/etc/profile.d/conda.sh" >> ~/.bashrc

# 测试
mamba --version

mamba create -p /home/handle/Applications/miniforge/envs/cosyvoice -y python=3.10

# 报'mamba' is running as a subprocess and can't modify the parent shell.
# Thus you must initialize your shell before using activate and deactivate.
# 就根据提示如执行下面的命令
eval "$(mamba shell hook --shell bash)"

mamba activate /home/handle/Applications/miniforge/envs/cosyvoice

# 删除环境
mamba env remove -p /home/handle/Applications/miniforge/envs/GPTSoVits

# 清理所有未使用的包和缓存
mamba clean -y --all
```

## NixOS

[NixOS笔记](/file/subnote/NixOS%20Note.md "点击链接查看NixOS笔记")
