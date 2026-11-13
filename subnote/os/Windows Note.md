
# Windows Note

## cmd命令

```cmd
# 查看端口占用情况
netstat -ano|findstr 端口号

# 根据进程id查看进程信息
tasklist|findstr 进程id

# 关闭进程
taskkill /F /PID 进程id
```

## host文件

hosts文件里可建立许多常用域名与其对应IP的映射。当用户在浏览器中输入一个想要浏览的网址时，系统会首先在hosts文件里面查找有没有对应的IP，若有的话，则会立即打开对应的网页；若是没有，则会请求DNS服务器进行解析

- hosts文件目录为在`C:\Windows\System32\drivers\etc\`

- hosts语法格式 1个IP对应1个主机名或域名，构成一组对应关系。一组对应关系占一行。IP在前，主机名或网址在后；IP与主机名间至少有1个空格。

```hosts
# 这行是注释
127.0.0.1 www.baidu.com
```

- 当我们在文件中写入“127.0.0.1+空格+你想屏蔽的网址”，或者是“0.0.0.0+空格+你想屏蔽的网址”就可以实现该网站的屏蔽
  
- cmd 输入 `ipconfig /flushdns` 让host文件生效
- cmd 输入 `ipconfig /displaydns` 显示系统的DNS域名解析缓存

## 校验下载的文件的Hash值

- 在文件所在位置鼠标右键->`Open Git Bash Here`

```sh
# SHA512可以换成别的hash方式，具体看校验的是什么码就用什么方式
certutil -hashfile filename SHA512
```

## 中州韵输入法

### 扩展现有词汇

- 1.新建文件`luna_pinyin_simp.custom.yaml`，加入如下内容

```yaml
patch:
    translator/dictionary: luna_pinyin.extension
```

- 2.新建文件`luna_pinyin.extension.dict.yaml`，在文件末尾追加词条

```yaml
# Rime dictionary
# encoding: utf-8

# 表示yaml文件开始
---
name: luna_pinyin.extension
version: "2025.09.29"
sort: by_weight
use_preset_vocabulary: true
# 從 luna_pinyin.dict.yaml 導入包含單字的碼表
import_tables:
  - luna_pinyin
# 表示yaml文件结束
...

# table begins
# 在下面添加自定義的詞條
垌 dong
㘃 re
```

- 3.将这两个文件放到用户文件夹下，然后重新部署即可
