# NixOS

## 基本概念

Nix：包管理器

Nixpkgs： Nix包仓库，这个地址包含了可用的包仓库版本：<https://channels.nixos.org/>

nixpkgs channel：指向某个版本的包仓库的引用，相当于NixOS channels的具体实例

- NixOS channels：是包管理器用来发布包定义和二进制包的机制
    - 理解成发布管道吧，分为：
        - Stable channels，如：nixos-25.11，通常是minor更新，比较保守，维护时间为直到下个稳定分支创建，系统更新推荐用这个发布管道

        - The unstable channel，如：nixos-unstable，对应NixOS的主开发分支，因此可能会在更新时看到激进的改动，不推荐在生产系统使用

        - Small channels，如： nixos-25.11-small，nixos-unstable-small，分别跟上述发布管道相对应
            - 它们包含的二进制包很少，更新比常规发布管道更快，但构建的时候可能需要更多的包
            - 它们包含很少的GUI应用，更适合用在服务器环境
    - 在安装NixOS的时候，会自动订阅跟系统版本对应的发布管道

```sh
# 查看订阅的发布管道
sudo nix-channel --list | grep nixos

# 切换发布管道，最后不要忘了写nixos参数
sudo nix-channel --add https://channels.nixos.org/channel-name nixos

# 升级系统到指定发布管道的最新版本
# 相当于：执行nix-channel --update nixos后执行nixos-rebuild switch
# 新系统往往有新的包管理器，将导致包管理器数据库方案的升级，这个不是简单能完成的
# 因此升级后往往不能回退到旧的发布管道了
sudo nixos-rebuild switch --upgrade
```

```nix
# 自动升级
# 将定期执行nixos-upgrade.service（systemd服务）
system.autoUpgrade.enable = true;
# 如果为false，在当前发布管道执行nixos-rebuild switch --upgrade
# 如果为true，如果新generation包含不同的内核，initrd或内核模块，系统将自动重启
system.autoUpgrade.allowReboot = true;

# 指定发布管道
system.autoUpgrade.channel = "https://channels.nixos.org/nixos-25.11";
```

核心配置文件：/etc/nixos/configuration.nix

```sh
# 修改核心配置文件后重建generations
# 刷新（重新加载）用户级 systemd 服务的配置文件，想要应用新的用户服务配置必须手动restart
sudo nixos-rebuild switch

# 重建配置并切换，但是不设置为默认引导项
# 因此如果配置锁了当前的系统，可以通过重启恢复到可用的配置
sudo nixos-rebuild test

# 重建配置并且设置为默认引导项，但是不立即切换（直到下次启动才真正生效）
sudo nixos-rebuild boot

# -p test，让配置在grub2的引导菜单项显示test，便于跟stable配置区分
sudo nixos-rebuild switch -p test

# repl，交互式解释器，可以实时查看调试NixOS配置
sudo nixos-rebuild repl

# 只构建generations，不切换
sudo nixos-rebuild build

# 测试新配置的另一种方式
# 根据当前的NixOS配置构建一个“可直接运行的虚拟机镜像”
# 但是这个虚拟机不会从物理机继承用户帐号和home目录
# 除非在configuration.nix设置：users.mutableUsers = false;
# 或在configuration.nix设置：users.users.your-user.initialHashedPassword = "test";
# 如果已经启动过build-vm生成的虚拟机，那么$hostname.qcow2里已经包含旧的用户数据
# 之后你再修改configuration.nix，虚拟机不会自动更新这些用户，除非你删除旧的qcow2文件
sudo nixos-rebuild build-vm
# 启动这个虚拟机
./result/bin/run-*-vm

# 端口转发只能访问虚拟机里绑定在 “0.0.0.0” 或 “虚拟网卡 IP” 上的端口
# 而不能访问只绑定在 127.0.0.1（loopback）上的端口
# 换句话说：如果VM里的服务只监听127.0.0.1，那么端口转发是连不进去的
# 宿主机和虚拟机端口映射，宿主机端口2222，虚拟机端口22
QEMU_NET_OPTS="hostfwd=tcp:127.0.0.1:2222-:22" ./result/bin/run-*-vm

# 通过ssh登录虚拟机
# 假设已经正确设置密码或ssh key，并且虚拟机的防火墙已经放行端口
ssh -p 2222 localhost

# 查看当前系统generations
sudo nix-env --list-generations --profile /nix/var/nix/profiles/system

# 切换到指定generations
sudo nix-env --profile /nix/var/nix/profiles/system --switch-generation 204

# 删除所有旧generations，只保留当前版本
sudo nix-collect-garbage -d

# 删除指定generations
sudo nix-env --profile /nix/var/nix/profiles/system --delete-generations 205 206

# 查看帮助
nixos-help
```

## 安装NixOS

- 图形化ISO镜像安装无脑点就行了，下面以最小化ISO镜像（或者是图形化ISO镜像进入live后选择终端手动）安装为例

- 下载iso、制作启动盘、进入live系统略过

- 切换root用户，设置字体，连接网络

```sh
# 进入live系统后，切换为root用户
# 当前自动登录的nixos用户密码是空的，sudo操作不用输入密码
sudo -i

# 如果觉得终端字体太小了
setfont ter-v32n

# 连接网络
nmtui
```

### 通过ssh远程安装（可选）

```sh
# 先开启sshd服务，在live系统执行
systemctl start sshd

# 设置root或nixos的密码，用来远程登录，以nixos为例
passwd nixos
```

复制客户机的ssh公钥文件到live系统的`/home/nixos/.ssh/authorized_keys` 或 `/root/.ssh/authorized_keys`目录下

- 分区定义、格式化、挂载略过，可参考archlinux的安装教程

- When the install is complete, remove the USB flash drive and reboot into your new system!

### 定义系统配置

- 生成配置文件

```sh
# 会在/mnt/etc/nixos目录下生成configuration.nix和hardware-configuration.nix文件
# 它生成的是最小可用配置，可以根据需要增加配置
sudo nixos-generate-config --root /mnt
```

根据需要决定是否编辑`/mnt/etc/nixos/configuration.nix`文件

#### 设置引导（了解，nixos-generate-config自动完成）

##### BIOS systems（很少用了）

```nix
# 指定GRUB引导加载器安装的磁盘，注意是设备而不是分区
boot.loader.grub.device = "/dev/sda";

# 如果有双系统以上
boot.loader.grub.useOSProber = true;
```

##### UEFI systems(主流)

- 用systemd-boot

```nix
boot.loader.systemd-boot.enable = true;
```

- 用GRUB

```nix
boot.loader.grub.device = "nodev";
boot.loader.grub.efiSupport = true;

# 如果有双系统以上，会检查windows系统
# 如果是双linux系统以上，GRUB在多Linux系统下容易出现配置冲突
# 官方建议用systemd-boot
boot.loader.grub.useOSProber = true;
```

#### 配置wifi（如果新系统要用Wi‑Fi）

```sh
# 使用networkmanager简化网络配置
networking.networkmanager.enable = true;

# 所有需要配置网络的用户必须加入networkmanager组
users.users.alice.extraGroups = [ "networkmanager" ]; 

# 然后就可以通过nmcli或nmtui来连接wifi了（对于gnome和kde桌面）
```

#### 配置国内加速

在挂载分区后安装之前，先配置国内加速，但是目前在虚拟机的效果是设置没有生效

```sh
# 编辑主配置文件
# 配置文件的内容在rebuild成功后的下一次rebuild才会生效，因此安装系统应该用临时指定国内源的方式
sudo vim /mnt/etc/nixos/configuration.nix

# 在imports = [ ./hardware-configuration.nix ];后添加
# 这个配置只对安装好的新系统生效
# 可以多写一些，因为有些包在某个源是没有的，而在另外的源可能有，如果都没有会去官网下载，不过会很慢
# 安装系统是指定国内源加速安装后面会给出
nix.settings = {
    substituters = [
        # 清华源，目前（26.04）未提供nix-darwin的二进制缓存，管网说请使用官方源或上海交大源
        "https://mirrors.tuna.tsinghua.edu.cn/nix-channels/store"
        # 上海交通大学源
        "https://mirrors.sjtu.edu.cn/nix-channels/store"
        # 中国科学技术大学
        "https://mirrors.ustc.edu.cn/nix-channels/store"
    ];
};
```

#### Secure shell (SSH)配置（可选）

```nix
services.openssh.enable = true; 

# 默认不允许root通过ssh登录，可以将其设置为no来允许，但不推荐
services.openssh.settings.PermitRootLogin = "no";

# PermitRootLogin不允许root通过ssh登录的情况下，可以通过这种方式允许通过SSH key登录root
# 当然普通用户也可以这么用
users.users.用户名.openssh.authorizedKeys.keys = [ "ssh-ed25519 AAAAB3NzaC1kc3MAAACBAPIkGWVEt4..." ];
```

### 开始安装

```sh
# 安装
# 配置文件的内容在rebuild成功后的下一次rebuild才会生效，因此安装系统应该用临时指定国内源的方式
# 通过选项临时指定源，多个源可以用空格分开，并且是仅生效你指定的源，因此应该将官方源也加上做兜底
# 如果不指定国内源安装会很慢
# 以后执行nixos-rebuild时也可以像这样临时指定源
# 如果配置文件报错，修改好后重新执行
# 中断安装（ctrl + c）不会损坏已下载内容
sudo nixos-install --option substituters "https://mirrors.tuna.tsinghua.edu.cn/nix-channels/store"

# 如果用的是基于flake的配置
# 配置文件：path/to/flake.nix
# default generated flake用nixos
nixos-install --flake 'path/to/flake.nix#nixos'

# 无监管安装，不会提示设置root密码
nixos-install --no-root-passwd
```

#### 设置root密码

安装最后会提示你设置root密码，根据提示操作即可

如果在configuration.nix文件定义了登录用户和密码

也要在这时候修改其密码，以handle用户为例

```sh
nixos-enter --root /mnt -c 'passwd handle'
```

注意：如果除了root用户没有添加其他用户，一些图形显示管理器如sddm默认是不允许root登录的

#### 重启电脑进入NixOS

如果所有安装步骤都完成了，就可以重启进入新系统了

```sh
reboot
```

## 安装英伟达显卡驱动

```nix
# 由于英伟达显卡驱动是闭源的，要先允许安装unfree包
nixpkgs.config.allowUnfree = true;

# 指定使用英伟达显卡驱动
services.xserver.videoDrivers = [ "nvidia" ];

# 如果显卡比较老旧，先去官网根据显卡型号查询驱动版本，如：580.xxx
# 然后查询NixOS配置集有没有对应的版本选项，如：config.boot.kernelPackages.nvidiaPackages.legacy_580
# 例如：执行nixos-option hardware.nvidia.package，会看到有580xxx的描述
# 如果有则填写上去
hardware.nvidia.package = config.boot.kernelPackages.nvidiaPackages.legacy_580;

# 让OpenGL支持32位的应用，如：wine
hardware.graphics.enable32Bit = true;
```

### 包管理

- 1.将需要的包写到configuration.nix的environment.systemPackages的选项里面（需要确认这个选项存在），不需要就从选项里移除就行了，然后执行`nixos-rebuild switch`让修改生效

```nix
environment.systemPackages = [ pkgs.thunderbird ];

# 通过nixpkgs.config选项来配置Nixpkgs
# 只对“使用这个 NixOS 配置构建出来的系统”生效
# 不影响nix-build、nix-env、nix-shell等命令
nixpkgs.config.allowUnfree = true;
```

```nix
# emacs有个依赖是gtk2，但是我想用gtk3，可以这样写
environment.systemPackages = [ (pkgs.emacs.override { gtk = pkgs.gtk3; }) ]; 
```

- 2.Ad hoc，通过`nix-env`命令安装、更新、卸载包，这种方式允许不同版本包仓库的包混合安装，它也是非root用户安装的选择

```sh
# 获取可用包列表
nix-env -qaP '*' --description | grep 关键字
```

## NixOS配置文件

- 基本结构

```nix
# config包含了所有模块整合后的配置
{ config, pkgs, ... }:

{
  # option definitions
  someName = someValue;
}
```

### 模块性

configuration.nix本身也是一个模块

当文件太大时，可以分成多个文件然后导入

或者也可以提取模块共同的内容到一个common.nix

```nix
{ config, pkgs, ... }:

{
    imports = [
        # 导入其它模块
        ./subfile1.nix
        ./subfile2.nix
    ];
    services.httpd.enable = true;
    # 对于列表类型的选项，如果在子模块也有定义，NixOS会合并，默认configuration.nix文件的选项会放在最后
    # 如果想要放到最前面，可以用mkBefore
    # 如：boot.kernelModules = mkBefore [ "kvm-intel" ];
    # 对于其它类型的选项，如果在子模块也定义了，则nixos-rebuild会报错
    # 可以使用pkgs.lib.mkForce设置优先级来解决
    # 如：services.httpd.adminAddr = pkgs.lib.mkForce "bob@example.org";
    environment.systemPackages = [ pkgs.emacs ];
    # ...
}
```

使用多模块配置的时候，某个配置项的值不能明显地知道，可以用nixos-option查看

```sh
nixos-option services.xserver.enable

# 也可以用交互式环境确认
nix repl '<nixpkgs/nixos>'

# 然后输入选项，就得到输入了
# 输出："具体hostName"
config.networking.hostName
```

### appimage支持配置

```nix
programs.appimage.enable = true;
programs.appimage.binfmt = true;

# 如果运行appimage时缺失共享库，将其添加到如下位置
programs.appimage.package = pkgs.appimage-run.override {
    extraPkgs = pkgs: [
      # missing libraries here, e.g.: `pkgs.libepoxy`
    ];
};
```

## nix-shell

### 命令式创建nix-shell环境

```sh
# nix-shell用来临时安装包，这个命令实际上是创建一个包含指定包名的nix-shell环境
# 安装成功后会进入nix-shell环境，提示符变成：[nix-shell:~]$，就可以运行安装的包了
# nix-shell环境中也可以执行安装命令，然后进入嵌套的nix-shell环境，它会继承上一层的应用
# 在嵌套的nix-shell环境中执行exit，会回到上一层nix-shell环境
nix-shell -p 包名 [包名2]

# 快速执行然后退出nix-shell环境
# 这个命令不会在nix-shell环境安装该包，如果系统没有该包会先拉取
# --pure，忽略大多数环境变量，相当于隔离运行
# -I，指定包源
nix-shell -p 包名 --run "包名[ 选项]"
nix-shell -p git --run "git --version" --pure -I nixpkgs=https://github.com/NixOS/nixpkgs/tarball/2a601aafdc5605a5133a2ca506a34a3a73377247

# 退出nix-shell环境，安装的包也会没了
exit

# 删除不同版本的包，释放占用的空间
# 实际上是删除Nix store中无用的构建结果
nix-collect-garbage
```

### 通过shell.nix配置nix-shell环境

- 创建shell.nix并输入如下内容

```nix
let
    nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-25.11";
    pkgs = import nixpkgs { config = {}; overlays = []; };
in

pkgs.mkShellNoCC {
    packages = with pkgs; [
        # 这里列出包名
        cowsay
        lolcat
    ];

    # mkShellNoCC中的属性名只要不是保留字段
    # 并且属性值能被转换成字符串，那么它就会变成环境变量
    GREETING = "Hello, Nix!";

    # 在进入nix-shell环境开始交互之前，执行一些命令
    # 也可以通过shellHook来使用即使是保留字的属性名
    shellHook = ''
        echo $GREETING | cowsay | lolcat
    '';
}
```

- 在shell.nix文件所在路径执行`nix-shell`来创建nix-shell环境

## 指定包版本

```sh
# <nixpkgs>指向文件系统的包，不可复现
{ pkgs ? import <nixpkgs> {} }:

# 指向特定版本的包
{ pkgs ? import (fetchTarball "https://github.com/NixOS/nixpkgs/archive/06278c77b5d162e62df170fec307e83f1812d94b.tar.gz") {}
}:
```

## nixos-kde的桌面快捷方式

如果直接在Application Launcher选择应用，鼠标右键添加到桌面，nixos-rebuild后，如果执行了nix-collect-garbage -d，则这些链接会失效

因为这些链接是指向旧的系统代的具体链接（带hash）

要解决这个问题，进入`/run/current-system/sw/share/applications`目录，将对应的桌面快捷方式文件拖到桌面就行了

## 使用Home Manager

### 配置home-manager

- 在/etc/nixos/configuration.nix中导入home-manager

```sh
let
    # home-manager作为模块添加到NixOS，笔者使用的是25.11版本的系统，这里也选择对应版本的home-manager
    home-manager = builtins.fetchTarball https://github.com/nix-community/home-manager/archive/release-25.11.tar.gz;
in
{
    imports = [
        # 导入home-manager
        (import "${home-manager}/nixos")
    ];

    # handle是普通用户
    users.users.handle.isNormalUser = true;

    # 定义handle用户的home-manager配置
    home-manager.users.handle = { pkgs, ... }: {
        # 定义要home-manager管理的用户级软件
        home.packages = with pkgs; [
            
        ];

        # 定义要home-manager管理的应用配置，如果home-manager没有对应的programs.appname模块，则需要自行声明，如用xdg.configFile
        programs.git = {
            enable = true;
            # 如果已经有了git的用户配置，执行home-manager switch会提示该配置的路径并让你先移动/删除它，不然这个配置不会生效
            userName = "handle";
            userEmail = "handle@example.org";
        };

        programs.vscode.enable = true;
        programs.idea.enable = true;
        programs.firefox.enable = true;
        programs.onlyoffice.enable = true;

        # The state version is required and should stay at the version you
        # originally installed.
        home.stateVersion = "25.11";

        # Let Home Manager install and manage itself.
        programs.home-manager.enable = true;
    };
}
```

### 激活home-manager

```sh
home-manager switch

# will create a result link to a directory containing an activation script and the generated home directory files
home-manager build

# 恢复到上一次的配置
home-manager switch --rollback
```

## 使用Nix Flakes

- 先在configuration.nix添加如下内容并重新构建系统

```sh
nix.settings.experimental-features = "nix-command flakes";
```

## Nix语言

```sh
# 装完NixOS操作系统后，可以在终端输入"nix repl"进入交互环境，有点像jshell，不过它只支持Nix语言
# 笔者就称它为nixshell吧
nix repl

# 退出nixshell
:q

# 帮助
:?

# 有些输出显示的不够完整，可以用:p
# 输出：{ a = { ... }; }
{ a.b.c = 1; }

# 输出：{ a = { b = { c = 1; }; }; }
:p { a.b.c = 1; }

# 1.定义file.nix
echo 1 + 2 > file.nix

# 2.计算file.nix的结果
# 如果不指定文件名，则默认读default.nix文件
# 有些输出显示的不够完整，可以用--strict
# 输出：3
nix-instantiate --eval file.nix

echo "{ a.b.c = 1; }" > file.nix
# 输出：{ a = <CODE>; }
nix-instantiate --eval file.nix
# 输出：{ a = { b = { c = 1; }; }; }
nix-instantiate --eval --strict file.nix

# 以下两种写法是一样的
# 输出：3
let
    x = 1;
    y = 2;
in x + y

# 输出：3
let x=1;y=2;in x+y
```

### 名称和值

Nix语言中，值可以是级别数据类型，列表，属性集和方法

属性集和let表达式都是用来给值分配名称的，用=赋值，结尾加分号

```sh
# 属性集是一个name-value-pairs，name必须唯一
{
    name =  "handle";
    age = 18;
}

# 递归属性集，允许在属性集内部的某个属性访问属性集的其它属性
# 如果不加rec就会报错
# 属性的顺序可以任意，输出时会进行排序
# 输出：{ one = 1; three = 3; two = 2; }
rec {
    one = 1;
    two = one + 1;
    three = two + 1;
}
```

### `let...in...`表达式

```sh
# let表达式：
# 将值赋给名称以重复使用，赋值顺序随意，可以引用其它的名称
let
    b = a + 1;
    a = 1;
in
a + b

# 只有let表达式里面的表达式可以相互访问，外面的不可以
# 报错，x没定义
{
  a = let x = 1; in x;
  b = x;
}

# 通过属性名.内部属性名来访问属性
# 输出1
let
    attrset = { x = 1; };
in
attrset.x
```

### `with ...; ...`表达式

访问属性集的属性时，可以省略属性集名称

```sh
# 输出：[ 1 2 3 ]
let
    a = {
        x = 1;
        y = 2;
        z = 3;
    };
in
# 等效写法：[ a.x a.y a.z ]
with a; [ x y z ]

# 作用域仅限紧跟着的分号
# 报错：x没定义
let
    a = {
        x = 1;
        y = 2;
        z = 3;
    };
in
{
    b = with a; [ x y z ];
    c = x;
}
```

### `inherit ...`表达式

避免内嵌作用域赋值相同属性名时重复写外层作用域的属性名

```sh
# 输出：{ x = 1; y = 2; }
let
    x = 1;
    y = 2;
in
{
    # 等价于：x = x; y = y;
    inherit x y;
}

# 属性集用：inherit (...) ...
# 输出：{ x = 1; y = 2; }
let
    a = { x = 1; y = 2; };
in
{
    # 等价于：x = a.x; y = a.y;
    inherit (a) x y;
}

# 在let表达式内部也可以用
# 输出：[ 1 2 ]
let
    # 等价于： 
    # x = { x = 1; y = 2; }.x;
    # y = { x = 1; y = 2; }.y;
    inherit ({ x = 1; y = 2; }) x y;
in [ x y ]
```

### `${ ... }`

就把它当成字符串占位符，只有代表字符串类型的属性名或表达式才允许，否则会报错

```sh
# 输出："hello Nix"
let
    name = "Nix";
in
"hello ${name}"

# 支持内嵌写法，但是会增加阅读难度，不推荐这么写
# 输出："no no no"
let
    a = "no";
in
"${a + " ${a + " ${a}"}"}"
```

### 多行字符串

如果多行字符串的每一行前面都有相同数量的空格，那么这些空格会被自动去掉

```sh
''
multi
line
string
''
```

### 查找路径

它是一个文件系统路径，取决于builtins.nixPath定义的值

官方建议生产代码避免使用查找路径，它是不可复现的

```sh
# /nix/var/nix/profiles/per-user/root/channels/nixpkgs
<nixpkgs>

# /nix/var/nix/profiles/per-user/root/channels/nixpkgs/lib
<nixpkgs/lib>
```

### 方法

Nix语言的方法是没有名称的，为匿名函数或者称之为lambda，但是它可以赋值给一个名称

```sh
# 方法定义，方法总是只有一个参数，用`: `（冒号空格）隔开，左边是方法参数，右边是方法体
functionArgument: functionBody

# 单参数方法
# 输出：<LAMBDA>，表示结果是一个匿名函数
x: x + 1

# 多参数方法，通过内嵌来实现
x: y: x + y

# 属性集作为方法参数
{ a, b }: a + b

# 给方法参数设置默认值
{ a, b ? 0 }: a + b

# 允许可变参数
{ a, b, ...}: a + b

# 给属性集参数命名，如下两种写法等价
args@{ a, b, ... }: a + b + args.c
{ a, b, ... }@args: a + b + args.c

# 将方法赋给一个名称
# 输出：<LAMBDA>
let
    f = x: x + 1;
in f
```

#### 方法调用

格式：方法（名） 参数，中间有空格

```sh
# 输出：2
let
    f = x: x + 1;
in f 1

# 字面量的属性集作为参数
# 输出：1
let
    f = x: x.a;
in
f { a = 1; }

# 名称作为参数
# 输出：1
let
    f = x: x.a;
    v = { a = 1; };
in
f v

# 通过括号调用匿名方法
# 输出：2
(x: x + 1) 1

# 括号表示方法调用，将结果作为列表元素
# 输出：[ 2 ]
let
    f = x: x + 1;
    a = 1;
in [ (f a) ]

# 由于列表元素也是用空格隔开，如果不用括号，会把f和a当成列表元素
# 输出：[ <LAMBDA> 1 ]
let
    f = x: x + 1;
    a = 1;
in [ f a ]

# 多参数方法通过内嵌方法实现，以下两种写法等价
x: y: x + y
x: (y: x + y)

# 输出：<LAMBDA>，相当于y: 1 + y
let
    f = x: y: x + y;
in
f 1

# 输出：3
let
    f = x: y: x + y;
in
f 1 2
```

##### 属性集参数

```sh
{a, b}: a + b

# 输出：3
let
  f = {a, b}: a + b;
in
f { a = 1; b = 2; }

# 当属性集实参多一个属性时，将报错调用意外参数c
let
  f = {a, b}: a + b;
in
f { a = 1; b = 2; c = 3; }
```

##### 参数默认值

格式：`参数名 ? 默认值`

如果一个参数定义了默认值，这个参数不是必传的

```sh
# 输出：1
let
    f = {a, b ? 0}: a + b;
in
f { a = 1; }

# 输出：0
let
    f = {a ? 0, b ? 0}: a + b;
in
f { } # empty attribute set
```

##### 可变参数

```sh
# 输出：3
let
  f = {a, b, ...}: a + b;
in
f { a = 1; b = 2; c = 3; }
```

##### 命名属性集参数的方法调用

```sh
# 输出：6
let
  f = {a, b, ...}@args: a + b + args.c;
in
f { a = 1; b = 2; c = 3; }
```

#### 方法库

有两个广泛使用的方法库：builtins和import

##### builtins

Nix自带了许多内置于该语言中的方法

它们是用C++实现的，作为Nix语言解释器的一部分

这些方法可以用builtins常量来使用

```sh
# 输出：<PRIMOP>
builtins.toString

# 输出："1"
builtins.toString 1
```

##### import

import接受指向Nix文件的路径，读取该路径以计算包含的Nix表达式，并返回结果值

如果该路径指向一个目录，则使用该目录中的default.nix文件

```sh
# 定义file.nix内容
echo 1 + 2 > file.nix

# 输出：3
import ./file.nix
```

由于nix文件可以包含任何表达式，import的是方法的话方法可以立即跟参数

```sh
# 定义file.nix内容
echo "x: x + 1" > file.nix

# 输出：2
import ./file.nix 1
```

##### pkgs.lib

nixpkgs仓库包含一个称为`lib`的属性集，它提供了大量有用的方法

并且是用Nix语言实现并作为Nix语言一部分

Nixpkgs属性集按照约定命名为pkgs，这些方法可以用pkgs.lib来使用

```sh
# 本例使用查找路径获取某个版本的Nixpkgs
# <nixpkgs>是查找路径，由环境变量$NIX_PATH的值决定，最终import这个值指向的文件
# 这里pkgs恰好是一个方法，因此给它传一个空的属性集作为参数就足够了
# 输出：LOOKUP PATHS CONSIDERED HARMFUL
let
    pkgs = import <nixpkgs> {};
in
# pkgs方法存在一个lib.strings.toUpper内嵌方法
pkgs.lib.strings.toUpper "lookup paths considered harmful"

# 本例用指定版本的Nixpkgs
let
    nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/archive/06278c77b5d162e62df170fec307e83f1812d94b.tar.gz";
    pkgs = import nixpkgs {};
in
pkgs.lib.strings.toUpper "always pin your sources"

# pkgs作为一个方法的参数的例子
{ pkgs, ... }: pkgs.lib.strings.removePrefix "no " "no true scotsman"

# 使用上面的方法
nix-instantiate --eval file.nix --arg pkgs 'import <nixpkgs> {}'

# 经常地，你还会在NixOS配置和Nixpkgs里面看到直接使用lib
# 在pkgs是可用的时候，这个lib是跟pkgs.lib是等价的
{ lib, ... }:
let
    to-be = true;
in
lib.trivial.or to-be (! to-be)

# 使用这个方法
nix-instantiate --eval file.nix --arg lib '(import <nixpkgs> {}).lib'


# 有时候为了提高易读性，pkgs和lib会作为参数传递
{ pkgs, lib, ... }:
# ... multiple uses of `pkgs`
# ... multiple uses of `lib`

# 由于历史原因，一些和pkgs.lib同名的builtins方法，功能是一样的
```

### 路径

每当在字符串插值中使用文件系统路径时，该文件的内容会被复制到文件系统中的一个特殊位置，即Nix store

然后计算字符串插值后，得到的字符串包含分配给该文件的Nix Store路径

说人话就是会复制字符串插值所指向的文件到Nix Store中，插值计算的结果是副本的路径`/nix/store/<hash>-<name>`

如果字符串插值指向的是一个路径，则整个路径（里面的文件和子目录）都会复制到Nix store，
插值计算结果变成该路径副本的路径

```sh
# 定义data文件的内容
echo 123 > data

# 插值表达式的计算结果必须是字符串
# 它代表对应的Nix store路径：/nix/store/<hash>-<name>
# 插值表达式会输出："/nix/store/h1qj5h5n05b5dl5q4nldrqq8mdg7dhqk-data"
"${./data}"
```

### Fetchers

作为构建输入的文件不是必须来自文件系统的

Nix语言提供了一些用于在求值期间从网络获取文件的方法

|序号|方法|
|:-|:-|
|1|builtins.fetchurl|
|2|builtins.fetchTarball|
|3|builtins.fetchGit|
|4|builtins.fetchClosure|

这些方法最终都会计算为文件在Nix store中的路径

```nix
# 输出："/nix/store/7dhgs330clj36384akg86140fqkgh8zf-7c3ab5751568a0bc63430b33a5169c5e4784a0ff.tar.gz"
builtins.fetchurl "https://github.com/NixOS/nix/archive/7c3ab5751568a0bc63430b33a5169c5e4784a0ff.tar.gz"

# 自动解压
# 输出："/nix/store/d59llm96vgis5fy231x6m7nrijs0ww36-source"
builtins.fetchTarball "https://github.com/NixOS/nix/archive/7c3ab5751568a0bc63430b33a5169c5e4784a0ff.tar.gz"
```

### Derivations

Derivations是Nix和Nix语言的核心，可以将其理解为派生构建（构建蓝图）

Nix语言用来描述derivations

Nix通过运行Derivations来获得构建结果

构建结果也可以作为其它derivations的输入

声明derivations的Nix语言原语是内置的非纯方法`derivation`

派生构建通常由Nixpkgs的构建机制`stdenv.mkDerivation`封装，它隐藏了关键的构建过程中涉及的许多复杂性

当你遇到`mkDerivation`时，它代表了Nix最终构建的一些东西

```nix
# pkgs.nix是一个derivation，这里可以将它看成一次derivation调用
# string插值将pkgs.nix转换为Nix store的路径
# 输出："/nix/store/sv2srrjddrp2isghmrla8s6lazbzmikd-nix-2.11.0"
let
    pkgs = import <nixpkgs> {};
in "${pkgs.nix}"
```

### 例子

```nix
# 声明nix的shell环境，并在环境初始化时执行shellHook定义的内容
{ pkgs ? import <nixpkgs> {} }:
let
    message = "hello world";
in
# pkgs.mkShellNoCC是一个方法
pkgs.mkShellNoCC {
    packages = with pkgs; [ cowsay ];
    shellHook = ''
        cowsay ${message}
    '';
}

# 例子：部分NixOS系统configuration.nix的代码
# 这个表达式是一个方法，返回一个属性集
# 方法参数最少要有config和pkgs，也可以有更多的参数
# 方法返回的属性集包含imports和environment
# 例子这里config参数没有用到
{ config, pkgs, ... }: {

    # imports是一个只有一个元素的列表，指向相对路径./hardware-configuration.nix的文件
    # 注意imports不是内置的import方法库，只是一个常规的属性名
    imports = [ ./hardware-configuration.nix ];

    # environment本身是一个只有一个属性（systemPackages）的属性集
    # systemPackages在这里是一个只有一个元素（git，即pkgs.git）的属性
    environment.systemPackages = with pkgs; [ git ];

    # ...

}

# 例子：简化Nixpkgs的包声明
# mkDerivation方法是stdenv的一个属性，接收一个递归属性集作为参数
{ lib, stdenv, fetchurl }:

stdenv.mkDerivation rec {

    pname = "hello";

    version = "2.12";

    # fetchurl来自外部方法的参数    
    src = fetchurl {
        url = "mirror://gnu/${pname}/${pname}-${version}.tar.gz";
        sha256 = "1ayhp9v4m4rdhjmnl2bq3cibrbqqkgjbl3s7yk2nhlh8vj3ay16g";
    };

    # meta属性本身是一个属性集
    meta = with lib; {
        # 即license = lib.licenses.gpl3Plus;
        license = licenses.gpl3Plus;
    };

}
```

## 打包

Nixpkgs Standard Environment (stdenv)

### derivation架构

```nix
# 这是一个需要一个属性集包含stdenv元素的方法
# 方法生成一个derivation（目前还啥也没有做）
{ stdenv }:

stdenv.mkDerivation {}
```

### 自定义一个打包方法（自包含stdenv依赖）

#### 打包逻辑

```nix
# default.nix
# 这个文件用来处理传参
let
    nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-25.11";
    pkgs = import nixpkgs { config = {}; overlays = []; };
in
{
    # 如果pkgs属性集的元素符合给定方法的参数，callPackage会自动传参给该方法
    # 这里是自动提供stdenv和fetchzip
    hello = pkgs.callPackage ./hello.nix {};
}

# hello.nix
{
    stdenv,
    fetchzip
}:
stdenv.mkDerivation {
    # 包名
    pname = "hello";
    # 包版本
    version = "2.12.1";

    # 从GNU Project’s FTP server下载源码归档文件用fetchzip
    # 指示Nix要用fetchzip来下载源码归档文件
    # fetchzip可以拉取的格式有很多，不仅仅是zip
    # fetchzip会自动解压，并对解压后的内容计算哈希
    src = fetchzip {
        # hello应用源码归档文件地址
        url = "https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz";
    # 在源码归档文件被下载和解压之前，是不知道hash是什么的
    # 如果提供一个错误的hashNix将会报错
    # 先将hash属性设置为空字符串，然后根据报错信息填写正确的hash
    sha256 = "sha256-1kJjhtlsAkpNB7f6tZEs+dbKd8z7KoNHyDHEJ0tmhnc=";
  };
}
```

#### 构建打包

```sh
# 实现hello.nix中定义的派生蓝图
# 从终端输出可知调用了configure方法，它用来生成一个Makefile，然后根据Makefile构建这个项目
# 由于stdenv构建系统是基于GNU Autoconf，它会自动检查项目路径构成，因此不需要写任何构建指示
nix-build -A hello
```

#### 构建结果

```sh
# 输出：default.nix hello.nix  result
# result是一个指向Nix store中，刚刚构建成功生成的二进制文件的路径的符号链接
ls

#  执行此二进制文件
./result/bin/hello
```

### 自定义一个打包方法（需要添加外部依赖）

- 打包逻辑

```nix
# default.nix
let
    nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-25.11";
    pkgs = import nixpkgs { config = {}; overlays = []; };
in
{
    icat = pkgs.callPackage ./icat.nix {};
}
```

```nix
# icat.nix
{
    stdenv,
    fetchFromGitHub,
    # 没有添加的话构建报错没有Imlib2.h
    # 如果到官网查询imlib2包会发现它在Nixpkgs
    imlib2,
    # 没有添加的话构建报错没有X11/Xlib.h
    # 但是由于官网的包名不一定就是Xlib
    # 因此安装nix-index包，然后执行"nix-locate Xlib.h"搜索是最快的
    # 输出：xorg.libX11: /include/X11/Xlib.h
    # 因此就知道是xorg.libX11了
    xorg
}:
stdenv.mkDerivation {
    # 包名
    pname = "icat";
    # 包版本
    version = "v0.5";

    # 从github下载源码归档文件用fetchFromGitHub
    # 指示Nix要用fetchFromGitHub来下载源码归档文件
    # fetchFromGitHub的参数是没有url属性的
    src = fetchFromGitHub {
        # 应用源码归档文件地址：https://github.com/atextor/icat/archive/refs/tags/v0.5.tar.gz
        owner = "atextor";
        repo = "icat";
        rev = "v0.5";
        # 在源码归档文件被下载和解压之前，是不知道hash是什么的
        # 如果提供一个错误的hashNix将会报错
        # 先将hash属性设置为空字符串，然后根据报错信息填写正确的hash
        # 或者用nix-prefetch-url直接计算hash然后填上去
        sha256 = "0wyy2ksxp95vnh71ybj1bbmqd5ggp13x3mk37pzr99ljs9awy8ka";
    };

    # 添加依赖buildInputs列表
    buildInputs = [ imlib2 xorg.libX11 ];

    # 解决了依赖问题，到最后还是会报错：make: *** No rule to make target 'install'.  Stop.
    # 因为stdenv尝试运行make install，Makefile是没有install目标的，icat仓库的README也没有提到安装的事情
    # 因此可以添加installPhase属性，它包含了很多命令来执行安装
    # 由于make已经成功完成，icat可执行文件已经生成到构建路径，只要将它复制到输出路径就可以了
    # 输出路径保存在$out变量中，这个变量在derivation’s builder执行环境是可访问的
    # 创建一个在$out目录下创建bin子目录，然后将icat二进制文件复制到里面（实际上就是安装了）
    installPhase = ''
        mkdir -p $out/bin
        cp icat $out/bin
    '';
}
```

```sh
# 获取hash，将获取到的hash设置到上面去
# --unpack，先解压，然后再对解压后的内容计算哈希
# --type sha256，计算算sha256
nix-prefetch-url --unpack https://github.com/atextor/icat/archive/refs/tags/v0.5.tar.gz --type sha256
```

- 构建打包

```sh
nix-build -A icat
```

- 构建结果

```sh
# 输出：default.nix icat.nix result
# result/bin/icat就是构建结果了
# 如果执行nix-build时不指定属性，如果hello.nix也在当前目录，并且default.nix也配置了hello的安装
# 将会按照default.nix文件中pkgs.callPackage的声明顺序，
# 得到：result/bin/hello，result-2/bin/icat
# 如果声明了n个pkgs.callPackage，就会有result-n/bin/somename
ls
```

## Phases and hooks

stdenv.mkDerivation派生构建被分成很多个阶段，每个阶段控制构建过程的某一方面

上面的例子，stdenv.mkDerivation自动完成了buildPhase，安装阶段手动定义了installPhase

在派生构建实现过程中，有很多shell方法（即hooks，在Nixpkgs中）会在每个派生构建阶段中执行，hooks做的事情有如设置变量，寻源文件，创建路径等

下面的例子是手动调用installPhase的相应hooks

```sh
# icat.nix

# ...
    installPhase = ''
        runHook preInstall
        mkdir -p $out/bin
        cp icat $out/bin
        runHook postInstall
    '';
# ...
```

### 使用总结

感觉是为了一个特点：声明式配置而牺牲了很多便利性，对老电脑的兼容性也没那么好

gnome+gdm桌面，输入密码后进入桌面，鼠标指针渲染成了一个灰白色的方框



解压缩能直接在archlinux上运行的vscode、idea等，在nixos上运行不了

- 英伟达显卡的安装和兼容问题，笔者的是gtx1060，试了很多个版本，最终能装的是535
    - kde+sddm桌面安装完显卡重启后直接黑屏
    - 最终各种尝试后发现kde+gdm在登录时选择x11可以进入桌面
        - 但是一旦在配置里面禁用gdm.wayland，开机就会卡住
        - 只能每次开机手动输入密码，然后选x11进入桌面
        - kde桌面快捷方式会在nix-collect-garbage -d后失效，因此会造成笔者只能选gnome桌面，但是gnome桌面的鼠标指针又有问题，就没法玩

usb wifi默认也识别成了CD-ROM，安装了usb-modeswitch也不能自己识别

需要每次开机都执行

```sh
sudo usb_modeswitch -v 0bda -p 1a2b -M "5553424312345678000000000000061b000000020000000000000000000000" -W
```
