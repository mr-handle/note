# Note

## 目录

- [后端笔记](/file/subnote/Java%20Note.md "点击链接查看后端笔记")

- [前端笔记](/file/subnote/Front%20End%20Note.md "点击链接查看前端笔记")

- [Linux笔记](/file/subnote/Linux%20Note.md "点击链接查看Linux笔记")

- [Windows笔记](/file/subnote/Windows%20Note.md "点击链接查看Windows笔记")

- [安卓笔记](/file/subnote/Android%20Note.md "点击链接查看安卓笔记")

- [Kotlin笔记](/file/subnote/Kotlin%20Note.md "点击链接查看Kotlin笔记")

## Markdown语法

```md
# 图片
![可选的图片描述，当图片不能被显示时而出现的替代文字](图片相对路径 "鼠标悬置于图片上会出现的文字，可以不写")

# 链接
[超链接显示名](超链接地址 "超链接title，当鼠标悬停在链接上时会出现的文字")
```

## 技嘉B360M POWER刷BIOS

- 去技嘉官网下载BIOS最新的更新包（包含了前面版本的所有更新），如`F17a`

- 解压更新包，得到`B360MPOWER.F17a`文件，将其复制到fat32文件系统的U盘的根目录下

- 重启电脑，进入BIOS，按下`F8`进入Q-Flash（Q‑Flash 是技嘉 BIOS 内置的刷写工具）

- 选择更新BIOS，然后选择U盘的`B360MPOWER.F17a`文件，选择一个更新模式，如fast更新或intact（完整）更新
    - fast更新：跳过部分 BIOS 镜像校验步骤，刷写速度更快，如果 BIOS 文件损坏、U 盘读写不稳定，Fast 模式更容易刷坏
    - intact（完整）更新：会对 BIOS 文件做完整校验，刷写前做更多安全检查，安全，但数度稍慢

- 刷写完成后会自动重启
    - 如果刷写完成后第一次自动重启后，显示一下主板logo后屏幕就只剩下了一个不闪烁的光标，等个十来分钟还是那样，就按电源键重启，然后进入BIOS
    - 如果Boot Option和Boot Overwrite不见了，就再刷一次BIOS更新，这次更新会比第一次更快，然后会自动重启两三次，然后会进到BIOS
    - 然后可以看到Boot Option和Boot Overwrite出现了，但是引导选项丢失了
    - 插上Live CD 启动盘，重写引导选项就可以了，如
        - 进入Live系统后先挂载系统分区然后挂载efi分区
        - 然后arch-chroot进入archlinux系统，执行grub-install安装grub引导，最后grub-mkconfig生成grub配置
        - 退出arch-chroot，卸载分区，最后重启就可以了
        - 进入BIOS，确认引导顺序，就可以重启然后正常进入系统了

## 国内AI使用心得

deepseek信息落后

- 文心一言
    - 会话上限比较少并且不能删除，每个会话会有提问上限，达到了提问上限就强制弹窗登录
    - 游客模式某个会话没有达到上限可以继续用不会弹登录弹窗
    - 有时候直接超时响应
    - 可以删除网站的cookie和站点设置数据来重置会话继续游客模式
    - 建议在浏览器设置里将其cookie和站点设置数据设置为会话期间有效（关闭浏览器就没了，省得每次手动删除）
    - 能显示推理过程
    - 有时候问几个问题就弹出登录弹窗了

- 千问
    - 游客模式会话上限比较多，达到上限后会强制弹窗让你登录
    - 可以删除会话，删除完也不可以继续游客模式了
    - 可以删除网站的cookie和站点设置数据来重置会话继续游客模式
    - 建议在浏览器设置里将其cookie和站点设置数据设置为会话期间有效（关闭浏览器就没了，省得每次手动删除）
    - 不能显示推理过程

紫东太初相应慢，网页版如果末尾不加`？`会吞掉末尾用户输入内容，并且部分对话不带问号就发不出去，会提示请输入问题，比较难用

## index-tts本地部署

官网：<https://github.com/index-tts/index-tts>

```sh
# 先安装git、git-lfs和uv
sudo pacman -S git git-lfs uv

# 启用lfs
git lfs install

# 下载源码
git clone https://github.com/index-tts/index-tts.git && cd index-tts

# 下载大文件
git lfs pull

# 安装依赖，这里指定了国内镜像（可选）
# --all-extras：安装全部可选功能。可去除自定义。
# --extra webui：安装WebUI支持（推荐）。
# --extra deepspeed：安装DeepSpeed加速。
uv sync --all-extras --default-index "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"

# 先设置镜像源，以防HuggingFace下载很慢
export HF_ENDPOINT="https://hf-mirror.com"

# 通过uv安装 HuggingFace CLI
uv tool install "huggingface-hub[cli,hf_xet]"

# 下载模型
hf download IndexTeam/IndexTTS-2 --local-dir=checkpoints

# PyTorch GPU 加速检测（可选），如果没有GPU加速就会用CPU
uv run tools/gpu_check.py

# 启动
uv run webui.py

# 启动完成访问http://127.0.0.1:7860
# 然后上传某个人的声音，然后指定文本，就可以生成语言了
```
