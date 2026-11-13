# ffmpeg

- 关于容器
    - 容器只是一个“文件封装格式”，规定视频、音频、字幕、元数据如何组织在一个文件里，容器只是提供一个放数据的“槽位”
    - mp4， 是ISO/IEC (国际标准组织)制定的标准容器，容器规范本身是开放标准，不收专利费
        - 对最新高分辨率特性支持有限，主流编码器都支持这个容器，更通用
    - mkv， Matroska ，是社区驱动的开源容器，BSD 风格许可，容器本身没有任何专利或版权限制
        - 支持 4K、HDR、多音轨（如普通话、粤语、英语）、多字幕（中文字幕、英文字幕、日语字幕）、章节标记，比mp4更多的编码器支持，更适合专业场景
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
# 等价于：-init_hw_device vaapi=vaapi0:/dev/dri/renderD128 -filter_hw_device vaapi0

# -vf/-filter:v，Video Filter（视频滤镜），这是在CPU解码时才需要的
# 告诉FFmpeg需要对输入的视频画面进行一系列的处理或特效加工（比如缩放、格式转换、上传到显卡等）
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
    -c:a libopus -b:a 160k \
    -c:v av1_vaapi -b:v 6000k \
    output.mp4

# 相比vaapi，vulkan用到的显存更多，但是生成视频的码率更接近设置的码率，编码速度更快，生成的文件更小，最推荐！！！
ffmpeg -init_hw_device 'vulkan=vk:0' \
    -i input.mp4 \
    -filter_hw_device vk -vf 'format=nv12,hwupload' \
    -c:a libopus -b:a 160k \
    -c:v av1_vulkan -b:v 6000k \
    output.mp4

# 方式二：如果已知原文件可以用gpu解码，编解码都用gpu（一条龙）
# -hwaccel vaapi，指定硬件解码器加速方式用vaapi，这样输入视频流会尽量用GPU的VAAPI 解码器来处理，而不是用CPU的软件解码器
# -hwaccel_output_format vaapi，指定硬件解码器的输出格式为 VAAPI surface（即 GPU 内存中的帧）
# 这样解码出来的帧不会先落到系统内存，而是直接保存在 GPU 的 VAAPI surface 中，方便后续滤镜或编码器继续在 GPU 上处理
# -hwaccel_device /dev/dri/renderD128，指定硬件加速解码时所使用的具体GPU设备节点
# 笔者的rx9070的测试结果是花的时间比方式一更多
ffmpeg -hwaccel vaapi -hwaccel_output_format vaapi -hwaccel_device /dev/dri/renderD128 \
    -i input.mp4 \
    -c:a libopus -b:a 160k \
    -c:v av1_vaapi -b:v 6000k \
    output.mp4
  
ffmpeg -init_hw_device 'vulkan=vk:0' -hwaccel vulkan -hwaccel_output_format vulkan -hwaccel_device vk \
    -i input.mp4 \
    -c:a libopus -b:a 160k \
    -c:v av1_vulkan -b:v 6000k \
    output.mp4

# 方式三：当原文件可能可以用gpu解码时
# -init_hw_device vaapi=foo:/dev/dri/renderD128，必须紧跟着ffmpeg
# 表示给硬件设备/dev/dri/renderD128（实际的 GPU 渲染节点文件）定义一个全局名称foo，之后可以用foo来引用它，而不用重复写路径
# vaapi表示驱动类型，将vaapi驱动绑定到这个硬件设备
# 笔者的rx9070的测试结果是花的时间比方式一更多，因为存在nv12|vaapi判断，花的时间比方式二还多
ffmpeg -init_hw_device vaapi=foo:/dev/dri/renderD128 -hwaccel vaapi -hwaccel_output_format vaapi -hwaccel_device foo \
    -i input.mp4 \
    -filter_hw_device foo -vf 'format=nv12|vaapi,hwupload' \
    -c:a libopus -b:a 160k \
    -c:v av1_vaapi -b:v 6000k \
    output.mp4
    
ffmpeg -init_hw_device 'vulkan=vk:0' -hwaccel vulkan -hwaccel_output_format vulkan \
    -i input.mp4 \
    -filter_hw_device vk -vf 'format=nv12|vulkan,hwupload' \
    -c:a libopus -b:a 160k \
    -c:v av1_vulkan -b:v 6000k \
    output.mp4

# -c:v av1_amf，使用av1_amf编码器（amd显卡）来编码视频流
# -quality，指定编码的质量/速度，取值有speed，balanced，quality，high_quality，编码的速度由快到慢，但是同码率下画质由低到高
# 它是av1_amf的私有选项，可以这样查看av1_amf的私有选项：ffmpeg -h encoder=av1_amf
ffmpeg -i input.mp4 -c:a libopus -b:a 192k -c:v av1_amf -quality balanced -b:v 6500k output.mp4

# rx9000系列显卡不支持vp8编解码加速，并且不支持mp4格式，同画质下，码率要是h264的1.1~1.3倍，文件更大了
ffmpeg -i input.mp4 -c:a copy -c:v libvpx -b:v 10783k output_vp8acc.mkv

# rx9000系列显卡加速不支持vp9编码加速，只支持解码加速，同画质下，码率要是h264 0.5倍到0.7倍，但是cpu生成比vp8更慢
ffmpeg -i input.mp4 -c:a copy -c:v libvpx-vp9 -b:v 6500k output_vp9acc.mp4
```

## 音频格式转换

```sh
# -sample_fmt s16：指定位深为s16（signed 16-bit integer），不指定的话默认是s32，根据源音频的位深进行设置就行了
# 如果是默认s32位深，FLAC 容器装不下 32 位的数据，于是它会自动将 32 位截断/降级为 24 位再封装进 FLAC 文件中
ffmpeg -i input.m4a -c:a flac -sample_fmt s16 output.flac
```

## 视频截取

视频并不是每一帧都是完整的画面。为了压缩体积，很多帧只记录了“和上一帧相比发生了什么变化”。只有“关键帧（I帧）”才是完整的画面。

当你用 -ss 00:01:30 指定一个起始时间，如果这个时间点不是一个关键帧，FFmpeg 为了保证你截取的视频能正常播放，它必须往前找，找到距离 00:01:30 最近的那个关键帧作为真正的起点

假设最近的关键帧在 00:01:28。FFmpeg 会把 00:01:28 的画面复制过来，但它依然认为这段视频的起点是 00:01:30。

于是，这段视频的前两帧的时间戳就变成了负数（比如 -2.000）

- 很多播放器（比如网页端的 `<video>` 标签、某些手机自带的播放器）在处理负数时间戳时会“懵掉”，常见的症状有：
    - 视频开头黑屏几秒钟
    - 音画不同步
    - 进度条拖动异常
    - 某些剪辑软件导入后报错

加上 `-avoid_negative_ts make_zero` 后，FFmpeg 会做一个“平移”操作：

它会检查整个视频的时间戳，如果发现最小的时间戳是负数（比如 -2.000），它就会给所有帧的时间戳都加上 2.000

这样，原本 -2.000 的那一帧就变成了 0.000，原本 0.000 的那一帧就变成了 2.000

整个视频的时间轴被整体往后平移了，但帧与帧之间的相对时间差完全没有改变

这是一个兼容性修复参数，不影响画质，不影响音画同步，只是让输出的文件更“标准”，在任何地方播放都不容易出问题

```sh
# 方法一
# avoid_negative_ts：避免（avoid）负数（negative）的时间戳（ts = timestamps）
# make_zero：把（时间戳）变成零
# -ss 00:01:30 放在-i之前，会在输入文件中跳转到指定位置
# 注意，在大多数格式中无法精确跳转，因此 FFmpeg 会跳转到该位置之前最近的跳转点（即关键帧），因此不够精确
ffmpeg -ss 00:01:30 -to 00:06:18 -i input.mp4   -c copy -avoid_negative_ts make_zero output.mp4

# 方法二
# -ss 00:01:30 放在输入文件后，输出文件前，会解码输入内容，但一直丢弃，直到时间戳达到指定位置
# 因此速度会变慢，但是精确，如果输入文件小或者要求精确可以使用这种方式
# 如果剪辑得到的目标视频时长比较短，也可以先用方法一粗略截取，然后用此方法再精确截取
ffmpeg -i input.mp4 -ss 00:01:30 -to 00:06:18 -c copy -avoid_negative_ts make_zero output.mp4
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
