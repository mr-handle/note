# git

## 安装

### Arch Linux

```sh
# 需要安装openssh才能使用ssh-keygen命令生成ssh密钥
sudo pacman -S git openssh
```

## 配置git

```sh
# 安装完成后，还需要最后一步设置，因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址
# global参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

# 然后查看git配置，看看刚刚的设置正确没有
# Windows
git config --global --list

# Arch Linux
cat ~/.gitconfig
```

## 生成SSH公/密钥

```sh
# 生成ssh key
# -t：ed25519表示指定密钥类型为Ed25519，rsa不再是首选了
# -C：注释
ssh-keygen -t ed25519 -C "这里填你的邮箱"

# 复制公钥
cat /path/to/id_ed25519.pub
```

## 创建版本库

- 创建一个空目录并进入这个空目录

```sh
mkdir -p 目录名
cd 目录名
```

- 通过git init命令把这个目录变成Git可以管理的仓库

```sh
git init
```

## 把文件添加到版本库

- 新建文件放到本地仓目录下
- 新建文件添加到仓库

```sh
# Git命令必须在Git仓库目录内执行。添加某个文件时，该文件必须在当前目录下存在
git add 新建文件名1 新建文件名2
```

- 把文件提交到仓库

```sh
git commit -m "自定义提交说明"
```

- 查看仓库当前状态

```sh
git status
```

- 查看文件修改了什么内容

```sh
git diff 文件名
```

- 知道文件修改了什么内容后，可以放心提交到仓库

```sh
git add 文件名
# commit之前可以再看看当前仓库状态，更放心提交
git commit -m "自定义提交说明"
```

- 从暂存区域移除，然后提交

```sh
git rm filename
```

- 文件重命名

```sh
# 这个命令相当于mv 旧文件名 新文件名、git rm 旧文件名、git add 新文件名 
git mv 旧文件名 新文件名
```

- 从一个服务器克隆一个现有的 Git 仓库并自定义本地仓库的名字

```sh
git clone url directoryname
```

```sh
# 查看提交历史
git log

# 按行展示log
git log --pretty=oneline

# 查看某个人的提交记录
git log --author=somebody

# 查看git历史执行过的命令
git reflog
```

- 撤销操作

```sh
# 尝试重新提交
git commit --amend

# 取消暂存的文件
git reset filename

# 撤消对文件的修改
git checkout --filename

# 丢弃本地的所有改动与提交，获取服务器上最新的版本历史，并将本地主分支指向它
git fetch origin

# 回退到上一个版本
git reset --hard HEAD^

# 回退到指定分支
git reset --hard origin/dev

# 回退到指定提交id
git reset --hard commitId
```

## 远程仓库

```sh
# 添加远程仓库
git remote add origin remoteUrl

# 远程仓库重命名
git remote rename origin person

# 移除远程仓库
git remote rm origin

# 将本地改动提交到远程仓库
git push origin branchName
```

## 分支操作

```sh
# 创建一个名字叫做 dev 的分支
git branch dev

# 切换当前分支为dev
git checkout dev

# 直接创建分支并切换过去
git checkout -b dev

# 合并分支
git merge dev

# 把新建的分支删掉
git branch -d dev

# 将分支推送到远端仓库
git push origin
```

## git常见问题

- Please commit your changes or stash them before you merge.

解决方法：通过git stash将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常git pull了，git pull完成后，执行git stash pop将之前本地做的修改应用到当前工作区。

```cmd
git stash
git pull
git stash pop
```

git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。

git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。
