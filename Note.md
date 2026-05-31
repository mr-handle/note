# Note

## 目录

- [后端笔记](/file/subnote/Java%20Note.md "点击链接查看后端笔记")

- [前端笔记](/file/subnote/Front%20End%20Note.md "点击链接查看前端笔记")

- [Linux笔记](/file/subnote/Linux%20Note.md "点击链接查看Linux笔记")

- [Windows笔记](/file/subnote/Windows%20Note.md "点击链接查看Windows笔记")

- [安卓笔记](/file/subnote/Android%20Note.md "点击链接查看安卓笔记")

- [Kotlin笔记](/file/subnote/Kotlin%20Note.md "点击链接查看Kotlin笔记")

## ffmpeg

- 关于容器
    - 容器只是一个“文件封装格式”，规定视频、音频、字幕、元数据如何组织在一个文件里，容器只是提供一个放数据的“槽位”
    - mp4， 是ISO/IEC (国际标准组织)制定的标准容器，容器规范本身是开放标准，不收专利费
        - 对最新高分辨率特性支持有限，主流编码器都支持这个容器，更通用
    - mkv， Matroska ，是社区驱动的开源容器，BSD 风格许可，容器本身没有任何专利或版权限制
        - 支持 4K、HDR、多音轨、多字幕、章节标记，比mp4更多的编码器支持，更适合专业场景
    - 主流的视频编码算法几乎都能同时封装到MP4和MKV容器里
    - 同参数只是容器不同生成的视频文件大小基本一样，生成时间也是一样的
    - 个人感觉mp4更商业化，mkv更加开源友好，但是mkv用ffprobe命令看不到音频的平均码率，而mp4可以，因此还是更建议用mp4
- 开源、免版税、可商用的视频编解码
    - 优先AV1（前身VP9），以下的了解一下就好了，26年3月杜比告了snapchat关于AV1侵犯其专利，目前还没有结果，先插眼
    - 现已推出AV2但是还没那么快普及，并且AV2需要的算力更加多
    - VP8（对标H.264）商用最安全，涉及的专利问题Google已与MPEG LA 和解，VP8 已经免版税，如果杜比胜了就看情况用VP8吧哈哈
        - libvpx
        - vp8_vaapi
    - VP9（对标H.265）曾被 Sisvel 专利池挑战，但不了了之，因此商用风险低一些
        - libvpx-vp9
        - vp9_vaapi

- 开源、免版税、可商用的音频编解码
    - 独立保存的音频优先flac，然后ALAC（苹果开源的），它们都是无损压缩的
    - 视频里面的音频编解码优先Opus，然后Vorbis（ogg，兼容旧生态，了解一下就行），它们都是有损压缩

- av1编码器
    - cpu编码器
        - libsvtav1，兼顾速度与画质，最推荐
        - librav1e，内存安全，速度和质量介于中间
        - libaom-av1，最慢，但画质最好
    - 显卡编码器
        - av1_amf，amd显卡编码器
- opus编码器
    - libopus（调用opus官方库）
    - opus，ffmpeg内置的简化实现，主要用于解码，编码质量和灵活性不如 libopus，不推荐用于编码
    - Opus 的比特率通常是AAC的60%~75%左右，128kbps的Opus≈192kbps的AAC（甚至接近 256 kbps），但是为了opus和aac互转的兼容性，统一设置为192k吧

- 综上就是视频文件用AV1和Opus，生成mp4格式就行了

```sh
# 查看视频文件的信息，包括音频和视频信息，码率、帧数等
ffprobe -i input.mp4

# -vaapi_device /dev/dri/renderD128，告诉 FFmpeg 用哪个 GPU 渲染节点，AMD固定这个写法

# -vf/-filter:v，Video Filter（视频滤镜），告诉FFmpeg需要对输入的视频画面进行一系列的处理或特效加工（比如缩放、格式转换、上传到显卡等）
# -vf 'format=nv12,hwupload'，先把画面转成显卡能听懂的nv12格式，再把它送进显卡的显存里准备开工，这里也是固定写法
# format=nv12，把视频画面的像素格式统一转换成nv12格式，VAAPI 编码器只接受NV12/P010格式，必须先转成 NV12再上传到GPU
# hwupload：把处理好的画面从系统内存（CPU）搬运到显存（GPU）里，交给显卡去编码

# -c/-codec，指定编解码器codec（coder/decoder），位于输入文件前表示解码器，位于输出文件前表示编码器，紧接着的:a或:v，指定音频流或视频流
# -c:a libopus，用libopus编码音频，也可以指定为copy，直接复制原视频的音频流
# -c:v av1_vaapi，使用av1_vaapi编码器（amd显卡）来编码视频流

# -b，指定比特率（码率），单位是k（bps）
# -b:a 192k，指定输出音频的比特率.
# -b:v 6500k，指定输出视频比特率
# 在保持相同画质的前提下根据原视频编码、原视频的码率和输出视频编码计算出一个值
# 比如原视频是H.264编码的，输出视频用AV1编码，那么用原视频的码率*(50%-70%，干脆折中取60%)作为目标码率
# 方式一：使用默认的解码器（cpu）解码原文件，然后上传帧给gpu，按指定编码器编码为输出文件
# 笔者的rx9070的测试结果是最适合用这个方式
ffmpeg -vaapi_device /dev/dri/renderD128 \
    -i input.mp4 \
    -vf 'format=nv12,hwupload' \
    -c:a libopus -b:a 192k \
    -c:v av1_vaapi -b:v 6500k \
    output.mp4

# 方式二：如果已知原文件可以用gpu解码，编解码都用gpu（一条龙）
# -hwaccel vaapi，指定硬件解码器加速方式用vaapi，这样输入视频流会尽量用GPU的VAAPI 解码器来处理，而不是用CPU的软件解码器
# -hwaccel_output_format vaapi，指定硬件解码器的输出格式为 VAAPI surface（即 GPU 内存中的帧）
# 这样解码出来的帧不会先落到系统内存，而是直接保存在 GPU 的 VAAPI surface 中，方便后续滤镜或编码器继续在 GPU 上处理
# -hwaccel_device /dev/dri/renderD128，指定硬件加速解码时所使用的具体GPU设备节点
# 笔者的rx9070的测试结果是花的时间比方式一更多
ffmpeg -hwaccel vaapi -hwaccel_output_format vaapi -hwaccel_device /dev/dri/renderD128 \
    -i input.mp4 \
    -c:a libopus -b:a 192k \
    -c:v av1_vaapi -b:v 6500k \
    output.mp4

# 方式三：当原文件可能可以用gpu解码时
# -init_hw_device vaapi=foo:/dev/dri/renderD128，必须紧跟着ffmpeg
# 表示给硬件设备/dev/dri/renderD128（实际的 GPU 渲染节点文件）定义一个全局名称foo，之后可以用foo来引用它，而不用重复写路径
# vaapi表示驱动类型，将vaapi驱动绑定到这个硬件设备
# 笔者的rx9070的测试结果是花的时间比方式一更多，因为存在nv12|vaapi判断，花的时间比方式二还多
ffmpeg -init_hw_device vaapi=foo:/dev/dri/renderD128 -hwaccel vaapi -hwaccel_output_format vaapi -hwaccel_device foo \
    -i input.mp4 \
    -filter_hw_device foo -vf 'format=nv12|vaapi,hwupload' \
    -c:a libopus -b:a 192k \
    -c:v av1_vaapi -b:v 6500k \
    output.mp4

# -c:v av1_amf，使用av1_amf编码器（amd显卡）来编码视频流
# -quality，指定编码的质量/速度，取值有speed，balanced，quality，high_quality，编码的速度由快到慢，但是同码率下画质由低到高
# 它是av1_amf的私有选项，可以这样查看av1_amf的私有选项：ffmpeg -h encoder=av1_amf
ffmpeg -i input.mp4 -c:a libopus -b:a 192k -c:v av1_amf -quality balanced -b:v 6500k output.mp4

# vp8不支持rx9000系列显卡加速，并且不支持mp4格式，同画质下，码率要是h264的1.1~1.3倍，文件更大了
ffmpeg -i input.mp4 -c:a copy -c:v libvpx -b:v 10783k output_vp8acc.mkv

# vp9不支持rx9000系列显卡加速，同画质下，码率要是h264 0.5倍到0.7倍，但是cpu生成比vp8更慢
ffmpeg -i input.mp4 -c:a copy -c:v libvpx-vp9 -b:v 6500k output_vp9acc.mp4
```

## 关于声音的知识

- 采样率 (Sample Rate)：每秒采多少次音频波形点，单位 Hz。比如 48,000 Hz 就是每秒采 48,000 个点
    - 奈奎斯特采样定理：要完整还原最高频率为 f 的信号，采样率必须 ≥ 2f
    - 人耳能感知的频率范围是 20Hz到20kHz → 理论上采样率至少要 40 kHz
    - 但是实际工程中不会选刚好 40 kHz，因为需要留出 滤波余量，避免高频 aliasing（混叠失真）
    - 常见采样率
        - CD 音频：44.1kHz
            - 因为早期数字录音机用视频磁带存储音频，和 NTSC/PAL 视频行频率匹配后得到的最合适采样率就是 44.1 kHz
            - 比 40 kHz 多出 4.1 kHz 的余量，方便滤波器在 20 kHz 附近逐渐衰减，避免混叠
            - 如果主要是录音乐或轻量级游戏音频，完全够用
        - DVD 音频：48kHz
            - 48 kHz 更方便和视频帧率（24/25/30 fps）对齐
            - 大多数显卡驱动、录屏软件、直播平台（OBS、B站、Twitch）默认就是 48 kHz，避免转码时音频和视频不同步
        - 专业录音：96kHz/192 kHz
            - 主要用于专业录音和后期制作，日常录屏和直播没有必要，反而增加 CPU/GPU 压力和文件大小

- 位深（Bit Depth）：每个采样点用多少位来表示振幅大小，它决定了音频的动态范围和精度
    - 16位音频能提供约 96分贝（dB） 的动态范围，而 24位音频的理论动态范围高达 144分贝
    - 人耳的听觉极限大约在 120分贝左右，而我们日常聆听音乐的房间（即使非常安静），其背景底噪通常也有 30到40分贝
    - 这意味着，16位的动态范围已经完全覆盖并远远超出了我们在普通房间里能听到的声音跨度
    - 24位多出来的那些极低噪音细节，早已被房间里的空调声、电脑风扇声等环境噪音彻底淹没了
    - CD 音频：16bit，平时用16位就够了
    - 专业录音：24bit
    - 位深越高，能表示的音量范围越大，采样点的数值越精细，声音细节更丰富，失真更小，量化噪声越低，安静部分更干净

- 声道数（单声道、立体声）

- 未压缩音频的比特率（bitrate）=采样率 (Hz)×位深度 (bits)×声道数，单位是 bit/s

- 48,000×16×2=1,536,000 bit/s≈1536 kbps

- 比特率是编码器设定的目标值（码率，每秒的数据量），比如 128 kbps、192 kbps、320 kbps。压缩算法会丢弃或简化部分信息，以减少数据量
    - 语音/播客：128 kbps
    - 日常音乐/视频/游戏录屏/直播：192 kbps，大多数人用普通耳机/音箱在 192 kbps 与 320 kbps 之间几乎听不出区别
    - 高保真音乐/专业场景：320 kbps或直接用 FLAC/WAV 无损

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

# 对于不能用显卡加速的情况，修改pyproject.toml
# 将url = "https://download.pytorch.org/whl/cu128"替换为
url = "https://download.pytorch.org/whl/cpu"

# 安装依赖，这里指定了国内镜像（可选）
# --all-extras：安装全部可选功能。可去除自定义。
# --extra webui：安装WebUI支持（推荐）。
# --extra deepspeed：安装DeepSpeed加速。
uv sync --all-extras --default-index "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"

# 先设置镜像源，以防HuggingFace下载很慢
export HF_ENDPOINT="https://hf-mirror.com"

# 通过uv安装 HuggingFace CLI
# uv tool install "huggingface-hub[cli,hf_xet]"

# 用huggingface下载模型
# hf download IndexTeam/IndexTTS-2 --local-dir=checkpoints

# 通过uv安装modelscope
uv tool install "modelscope"

# 用modelscope下载模型（推荐）
modelscope download --model IndexTeam/IndexTTS-2 --local_dir checkpoints

# PyTorch GPU 加速检测（可选），如果没有GPU加速就会用CPU
uv run tools/gpu_check.py

# 启动
uv run webui.py

# 启动完成访问http://127.0.0.1:7860
# 然后上传某个人的声音，然后指定文本，就可以生成语音了
```

```sh
# ImportError: TorchCodec is required for save_with_torchcodec. Please install torchcodec to use this function.
uv pip install torchcodec --index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

## coqui-TTS本地部署

```sh
git clone https://github.com/coqui-ai/TTS.git

cd TTS

uv venv

uv pip install -e .[all] --index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

# 注意tacotron2-DDC-GST不能生成英文，生成英文要用英文的模型
# tts_models/zh-CN/baker/tacotron2-DDC-GST会报错
# 如：In PyTorch 2.6, we changed the default value of the `weights_only` argument in `torch.load` from `False` to `True`....
# 这时候设置环境变量，执行export TORCH_FORCE_NO_WEIGHTS_ONLY_LOAD=1，后面就可以正常生成了
# 或者根据报错信息：TTS/utils/io.py", line 54, in load_fsspec return torch.load(f, map_location=map_location, **kwargs)
# 直接改这一行为return torch.load(f, map_location=map_location, weights_only=False, **kwargs)
# 推荐后面这一种写法，目前只有这个模型是可商用的中文模型
uv run tts --text "你好，世界！我是爱坤，喂我花生！" --model_name "tts_models/zh-CN/baker/tacotron2-DDC-GST" --out_path ~/Downloads/output.wav

# tts_models/en/multi-dataset/tortoise-v2：这个模型很大并且生成语音文件要好久，不推荐使用
# tts_models/en/ljspeech/glow-tts：生成速度块并且不会漏字，推荐
uv run tts --text "hello,world! I'm kunJack!" --model_name "tts_models/en/ljspeech/glow-tts" --out_path ~/Downloads/output.wav

# tts_models/multilingual/multi-dataset/your_tts可以将自己的声音作为输入，例子暂时不列出来，目前没有那个需求
uv run tts --text "hello,world! I'm aikun!" --model_name "tts_models/multilingual/multi-dataset/your_tts" --out_path ~/Downloads/output.wav --speaker_idx "female-en-5" --language_idx "en" 

# 列出多人语音模型的可选语音列表
uv run tts --model_name "tts_models/multilingual/multi-dataset/your_tts" --list_speaker_idxs

# 列出多人语音模型的可选语言列表
# 'female-en-5': 0, 'female-en-5\n': 1, 'female-pt-4\n': 2, 'male-en-2': 3, 'male-en-2\n': 4, 'male-pt-3\n': 5
uv run tts --model_name "tts_models/multilingual/multi-dataset/your_tts" --list_language_idxs

# 当使用某个模型报错时比如说：from transformers import LogitsWarper失败，就看依赖文件选个没有大改变的稳定版本安装
uv pip install transformers==4.36.2 --index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

# 当某个模型用uv run tts --list_models 显示已下载但是又报错说找不到就将该模型文件夹删了
rm -rf ~/.local/share/tts/问题模型
```
