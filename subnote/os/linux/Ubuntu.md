# Ubuntu

- 查看系统版本信息

```sh
# 简要信息
lsb_release -a

# 详细信息
cat /etc/os-release
```

## apt包管理器

apt：Advanced Packaging Tool

### 修改apt下载源

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

### apt常用命令

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
