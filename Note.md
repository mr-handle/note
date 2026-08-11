# Note

## 目录

- [Java笔记](/file/subnote/java/Java%20Note.md "点击链接查看Java笔记")

- [前端笔记](/file/subnote/Front%20End%20Note.md "点击链接查看前端笔记")

- [Linux笔记](/file/subnote/Linux%20Note.md "点击链接查看Linux笔记")

- [Windows笔记](/file/subnote/Windows%20Note.md "点击链接查看Windows笔记")

- [安卓笔记](/file/subnote/Android%20Note.md "点击链接查看安卓笔记")

- [Kotlin笔记](/file/subnote/Kotlin%20Note.md "点击链接查看Kotlin笔记")

- [Python笔记](/file/subnote/Python%20Note.md "点击链接查看Python笔记")

- [AI笔记](/file/subnote/ai/AI%20Note.md "点击链接查看AI笔记")

- [C/C++笔记](/file/subnote/cPlusPlus/C%20Plus%20Plus%20Note.md "点击链接查看C/C++笔记")

## 硬件笔记

### CPU

电脑主板的晶体发生器（晶振）提供100Mhz的基础频率，CPU为了和其他设备协调工作，需要把外频设置到100Mhz，CPU为了提高它的性能的同时还不影响到其他设备，就在外频的基础上翻倍，翻倍的数值就是倍频。
CPU主频（总频率）=CPU外频*CPU倍频

频率会影响到CPU性能，而频率是Intel或者AMD人为设定的，只要他们不锁定频率，我们就可以人为的修改这个频率，把频率往高了拉，就是超频。

- 超频前提条件：
    - 1.CPU不锁频率
    - 2.主板具备超频功能
    - 3.为超频准备更好的电源和主板

- CPU超频：
    - 1.超倍频（只影响CPU本身，不稳定因素少很多，只需专注调整CPU的参数）
    - 2.超外频（整个电脑系统的外频都要跟着变动，所有设备性能都提升，但是牵连设备多，只要有任何一个设备不能正常工作在新的外频下，电脑就会出现不稳定现象，轻则蓝屏死机，重则直接烧毁）

除了CPU外，内存也有相应的外频和倍频，如内存2400Mhz=100Mhz（外频）*24（倍频）

2133Mhz=133.33Mhz*16

2666Mhz=133.33Mhz*20

这涉及到内存外频异步工作，为了提高电脑性能，工程师研究出一种方法让内存的外频比CPU外频高33.33无穷的频率来运行，即让内存的外频工作在133.33Mhz频率下，为了实现这项功能需要内存的频率和基础频率异步运行，这就是内存频率转化率Ratio，BIOS中叫内存速度比率模式，有100:100（同步运行）、100:133（异步运行）

显卡核心频率、显存频率都跟主板基础频率没有关系，因为显卡是利用主板上的PCIE通道与CPU进行数据交互，PCIE是接在CPU内部的PCIE控制器（北桥）上的，早些年的时候北桥还未从主板上集成到CPU内，那时候可以借助超北桥频率来提高显卡性能，目前北桥已经集成到了CPU内部，显卡的核心频率和外频现在是没有多少关系的，有关系的是PCIE通信部分，所以显卡频率设定任何数值都可以，不必非是外频的整数倍；显存频率同理，和外频也没关系。

### 主板

- 主板：
    - 1.Intel不支持超频的主板：H系列和B系列；支持超频的主板是Z和X系列
    - 2.AMD全系列板子都能给内存超频，A系列板子不能超CPU，B系列和X系列是CPU和内存都可以超频
    - 3.可以查主板的内存支持QVL列表（qualified vendor list），QVL列表是厂家测试的各种型号的各种厂家的内存与这个主板的兼容性，最高可以使用多少频率多少根的内存。

### 内存

![内存颗粒描述](/image/内存颗粒描述.png)

- 内存频率：
    - 1.在主板不支持超频的情况下，内存频率上限由CPU默认内存频率决定
    - 2.在主板支持超频的情况下，CPU默认的内存频率会直接被无视掉，此时的内存频率限制因素只有三个：
        - 1.内存体质（由内存颗粒本身决定）
        - 2.IMC（内存控制器，在CPU中，Intel的IMC体质会比较好）性能/体质，内存是由IMC控制的，IMC性能直接决定可以使用多高频率的内存。
        - 3.主板布线与电气性能。

### 硬盘

- 机械硬盘种类：
    - 1.LMR 水平式记录磁盘（最早期）
    - 2.PMR 垂直式记录磁盘（工艺升级后，相同面积单盘容量提升）
        - 1.SMR瓦楞式堆叠磁盘 （可以存更多数据，但是写数据弱鸡的市面标缓存256MB，大于1T的机械硬盘）
        - 2.CMR（PMR传统磁盘）（良心，市面标的缓存一般还是64MB的机械硬盘）

机械硬盘：直接写数据覆盖原来数据

固态硬盘：先擦除再写数据（写数据会慢，TRIM回收指令：为了提高写数据的速度，删除数据后，若磁盘没有数据读写即空闲时，就会擦除之前删除的数据）

TRIM状态查询命令：fsutil behavior query disabledeletenotify

TRIM关闭命令：fsutil behavior set disabledeletenotify 1

TRIM打开命令：fsutil behavior set disabledeletenotify 0

磁盘阵列：多块磁盘如果不组磁盘阵列的话，磁盘与磁盘自己是完全没有任何干系的，除非同时调取所有磁盘数据，否则总有磁盘是空闲的，无形间浪费了一部分磁盘的性能。

raid0：一份完整的数据拆分开分散到N块磁盘里面，读写的时候同时往N块磁盘读写，带宽翻倍，速度翻倍，有多少磁盘翻多少倍性能（连续读写性能，随机的不变甚至由于模块磁盘拖后腿导致木桶短板效应性能降低），最终可以把南桥带宽跑到上限。raid0是所有raid中速度最快也是最不安全的，如果有任意一款磁盘故障，便无法正常读取，导致所有数据全部报废。

raid1：把一份完整的数据复制到其他磁盘里去，N块磁盘都会存入完全一样的数据。raid1是所有raid中最安全的，但是空间利用率和速度最低。为了让所有磁盘物尽其用发挥全部性能，就有了磁盘阵列。在电脑中称为raid。

raid2：利用海明码校验，主要目的是在raid0的基础上增加数据纠错能力，具有和raid0相似的并发性能，但是由于读写还需计算校验码用于纠错，实际上性能开销还是比较大的。使用raid2的人是比较少的。

raid3:与raid0、1、2至少需要2块磁盘不同，raid3至少需要3块磁盘。raid3相对于raid0，容灾能力从零块增加到了1块，由于多了一块校验码磁盘，与raid2那种一下一堆纠错码不同，恢复码的性能还是消耗比较少的，性能非常接近raid0。

raid4：和raid3很像，也是raid0的基础上增加一块恢复码磁盘。区别是raid3 是讲一个数据块拆分分开存，校验码也是针对拆分的部分去进行计算；而raid4是直接按区块进行存储，校验码是针对几个块一起进行计算，容灾能力也是一块磁盘。

raid5：raid4有自己的问题，n块磁盘都是只有一块恢复盘，数据盘越来越多的时候只有一块恢复盘的话，恢复盘的性能可能会制约整个阵列的性能，恢复盘的恢复数据块没有写完，下一次写入是无法进行的。raid5吧原本要存入恢复盘的恢复码直接拆分开，每一块磁盘都分别存储一部分恢复码，写入操作拆分，一方面可以做到raid0相似的性能，还增加了一块磁盘的容灾能力，还解决了raid4恢复码磁盘的瓶颈。目前raid5是民用层面使用最多的一种磁盘raid。

raid6：raid345都只能容灾一块磁盘的故障，还是不安全，于是raid6的恢复码相对于raid5从1组变成了两组，让raid6有了两块磁盘的容灾能力。

raid7：。。。。暂无数据

还可以进行raid嵌套：比如两个磁盘组raid1，在用另外两个磁盘组raid1，再把这两个raid1组成一个raid0，这就是我们所说的raid10；或者每三块磁盘组一个raid5，这三个raid5组建成一个raid0，就是raid50。

### 驱动

驱动程序：一种介于硬件和系统之间的API接口，让系统知道硬件的存在，并能通过这个API接口和硬件交互数据。

操作系统安装包虽然很大有几个G，但其系统本体是很小的，其它相当一部分数据都是兼容驱动，这些驱动可以在刚做好系统补齐专用驱动之前，提供一个最基本的API接口，让部分硬件能临时凑合用一下，方便用户去补齐这些硬件的专有驱动。不然的话，最基本的显卡驱动都没有，显示器是黑屏的，鼠标/键盘都没有反应，用户就无法补驱动。系统自带的兼容性驱动只能维持硬件的基本运行，让他们能够运作起来但并不能发挥全部的性能，所以一定要自己补齐各个硬件的专有驱动！

- 安装驱动有三种方法：
    - 1.windows10系统第一次开机后，系统如果检测到电脑是联网的状态，而且集成的网卡驱动可以正常驱使网卡联网，win10会自动帮用户开始下载安装驱动，用户只需把电脑丢一旁放一段时间就好，系统会自动帮用户把所有驱动全部装好。
        - 缺点是系统只会补齐必备硬件的驱动，其二是系统帮识别的驱动型号不一定有那么准，性能不一定是最大化，并且驱动版本不是最新的。不推荐这种方法。
    - 2.使用第三方软件帮用户安装。
        - 相对于win10自动安装的好处是驱动版本肯定是最新的，但是型号识别依旧不是100%准确，同样也检测不到非核心不见需要安装的驱动。此外这些第三方软件还可能流氓捆绑，而且第三方提供的驱动有没有动过手脚我们也是不知道的。非常不推荐
    - 3.自己手动官网找驱动自己装。强烈推荐用这种方法。

### 装机

- 装机：
    - 1.内存条插槽：左边为CPU ，从左往右数2、4优先插槽，1、3为次要插槽。一个内存插第二槽。不推荐插3跟内存，因为三根内存会导致系统略微的不稳定。
    - 2.显卡务必安插到距离CPU最近的PCIE*16槽当中。

- M.2固态硬盘两种接口：
    - 1.B-Key接口，比较老，速度比较慢，上方6pin，下方5pin，豁口在左边。
    - 2.M-Key接口，比较新，速度比较快，上方5pin，下方4pin，豁口在右边。

- 风扇：CPU散热风扇电源接口要接在CPU Fan1接口。

- 电源线缆接口：
    - 1.CPU供电接口：4pin+4pin
    - 2.主板供电接口：20pin+4pin，最宽最大
    - 3.显卡供电接口：2pin+6pin，显卡供电一般是两个2+6一组
    - 3.sata供电接口：扁扁的，接口形状类似英文字母L，又称扁L sata供电接口，给sata接口的固态硬盘和机械硬盘供电
    - 4.大4pin供电接口：形状和字母d一样，又称大4d接口，几乎所有的外围设备，比如说灯条RGB灯板，风扇等都可以从这个接口取电，并且可以转换成其他类型的供电，又称万能供电接口

## ffmpeg

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

### 视频截取

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

## deno

github：<https://github.com/denoland/deno>

下载后解压放到某个目录并设置到PATH即可

优势：进程内通信

劣势：打包体积会包含谷歌V8引擎，多出来70M

```sh
deno --version
```

## tauri

tauri目前展示页面时，标题栏的最小化，最大化和关闭按钮渲染有问题

```sh
# 笔者目前的电脑显示只用安装wget和xdotool
sudo pacman -S --needed \
  webkit2gtk-4.1 \
  base-devel \
  curl \
  wget \
  file \
  openssl \
  appmenu-gtk-module \
  libappindicator-gtk3 \
  librsvg \
  xdotool
```

## Neutralinojs

相比于Electron，Neutralinojs的打包体积更小，运行的内存更少，运行速度看官方的对比也是更少的运行时间

- 安装neu命令行工具

```sh
npm install -g @neutralinojs/neu
```

- 也可以不安装neu命令行工具，用npx执行对应的neu命令就行了

```sh
# 例：npx @neutralinojs/neu create myapp
npx @neutralinojs/neu <command>
```

### 使用neu

#### 使用普通js构建Neutralinojs应用

```sh
# 创建新项目
neu create 项目名

cd 项目名

# 运行项目
neu run

# 构建项目
# --release：将二进制文件打包到.zip文件
neu build --release
```

#### 使用前端框架构建Neutralinojs应用

##### 使用neu命令行工具已有的模板创建Neutralinojs应用

```sh
# 例：创建react应用
neu create myapp --template codezri/neutralinojs-react

cd myapp

# Start the React development server with Neutralinojs
neu run

# Build the React and Neutralinojs app
neu build
```

##### 使用任意前端框架构建Neutralinojs应用

以react前端框架为例

```sh
# 首先用 neutralinojs/neutralinojs-zero 模板，创建一个空的Neutralinojs项目
# 可能需要魔法
neu create myapp --template neutralinojs/neutralinojs-zero

# 然后进入项目
cd myapp

# 然后使用你最喜欢的前端框架创建一个新的项目
# 例1：创建react项目
npx create-react-app react-src

# 例2：创建vue项目，默认的项目名为vue-project
npm create vue@latest

# 然后配置 Neutralinojs 项目
# 由于neutralinojs-zero 创建了一个目前不需要的www目录，把它删了
rm -rf www

# 配置Neutralinojs项目以支持前端框架
# 打开neutralino.config.json
# 设置documentRoot的目录为你的前端框架的构建输出目录
# 这样Neutralinojs应用就知道前端框架的资源位置了
# 例1：react通常生成build输出到build目录，因此设置如下
"documentRoot": "/react-src/build/"

# 例2:vue的构建输出目录
"documentRoot": "/vue-project/dist/"

# 从前端框架的默认资源目录下加载一个图标
# 例1：react
"modes": {
    "window": {
        // --- other options
        "icon": "/react-src/public/logo192.png"
    }
}

# 例2：vue
"modes": {
    "window": {
        // --- other options
        "icon": "/vue-project/public/favicon.ico"
    }
}
```

默认情况下，neutralinojs-zero模板配置请求neu命令行工具从github发布中下载Neutralinojs客户端（即neutralino.js）

然后neu命令行工具通过复制neutralino.js来创建你的应用包

然而，你可以从npm仓库下载neutralino.js并和你的应用前端捆绑在一起

```json
//  例1：react
"cli": {
    // --- other options
    "resourcesPath": "/react-src/build/",
    // ---
    "clientLibrary": "/www/neutralino.js", // <--- 删除此属性以避免从github发布中下载neutralino.js
    // ---
}

//  例2：vue
"cli": {
    // --- other options
    "resourcesPath": "/vue-project/dist/",
    // ---
    "clientLibrary": "/www/neutralino.js", // <--- 删除此属性以避免从github发布中下载neutralino.js
    // ---
}
```

至此，你可以像Neutralinojs应用那样构建和运行react应用了

- 例1：构建和运行react应用

```sh
# 进入react项目
cd react-src

# 构建react应用
npm run build

# 进入Neutralinojs项目
cd ..

# 运行Neutralinojs应用
neu run
```

- 例2：构建和运行vue应用

```sh
# 进入react项目
cd vue-project

# 安装依赖包
npm i

# 构建应用
npm run build

# 进入Neutralinojs项目
cd ..

# 运行Neutralinojs应用
neu run
```

#### 用@neutralinojs/lib初始化本地API

你可以通过`neu run`运行应用，但你还不能使用本地API，因为它还没初始化

可以使用如下命令安装Neutralinojs客户端来初始化本地API

```sh
# 例1：react
cd react-src

# 例2：vue
cd vue-project

# 安装
npm install @neutralinojs/lib
```

接下来是加载Neutralinojs的全局变量

你可以用你选择的框架，通过在根html文件中包含JavaScript脚本来实现

react的根html文件通常在`./public/index.html`，可以添加如下代码来加载这个客户端库

vue的根html文件通常在`./index.html`，可以添加如下代码来加载这个客户端库

```html
<script src="%PUBLIC_URL%/__neutralino_globals.js"></script>
```

接下来是确保这个客户端库从你的前端应用入口文件那里初始化了

react的应用入口文件通常是`./src/index.js`

vue的应用入口文件通常是`./src/main.js`

因此，初始化过程可以在该文件，通过调用@neutralinojs/lib包的init方法进行

- 例1:react

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

// Import init function from "@neutralinojs/lib"
import { init } from "@neutralinojs/lib"

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

init(); // Add this function call
```

- 例2:vue

```js
import './assets/main.css'

import { createApp } from 'vue'
import App from './App.vue'

// Import init function from "@neutralinojs/lib"
import { init } from "@neutralinojs/lib"

createApp(App).mount('#app')

init(); // Add this function call
```

然后验证客户端库是否正确加载了

咱们通过用filesystem API读取Neutralinojs应用的当前目录来进行验证

首先在neutralino.config.json中更新允许Neutralinojs应用调用的api

可以允许filesystem整个命名空间的api调用（filesystem.*）

或者只允许指定命名空间的单个api调用，如下

```json
    "nativeAllowList": [
        "app.*",
        "filesystem.readDirectory"
    ],
```

对于react，接下来在`./src/App.js`添加如下代码

```js
import { useEffect } from 'react'
import './App.css';

// Import filesystem namespace
import { filesystem } from "@neutralinojs/lib"

function App() {

  // Log current directory or error after component is mounted
  useEffect(() => {
    filesystem.readDirectory('./').then((data) => {
        // 打印当前目录
      console.log(data)
    }).catch((err) => {
      console.log(err)
    })
  }, [])

  return (
    <div className="App">
      My Neutralinojs App
    </div>
  );
}

export default App;
```

对于vue，接下来在`./src/App.vue`添加如下代码

```vue
<script setup>
  // 其它内容省略

  // Import filesystem namespace
  import { filesystem } from "@neutralinojs/lib"

  import { onMounted } from 'vue'

  onMounted(() => {
    filesystem.readDirectory('./').then((data) => {
      console.log(data)
    }).catch((err) => {
      console.error(err)
    })
  })
</script>
```

最后运行Neutralinojs应用

```sh
# 例1：react
cd react-src

# 例2：vue
cd vue-project


npm run build

cd ..

# --window-enable-inspector：允许你打开开发者工具
# 在Neutralinojs应用的任意位置鼠标右键然后`inspect element`就可以打开开发者工具了
neu run -- --window-enable-inspector
```

#### 热重载

前端框架可以使用HMR (Hot Module Replacement)特性来提高开发效率

但是对于使用了前端框架的Neutralinojs应用，有两个http服务被启动

一个是Neutralinojs资源服务，另一个是前端框架的开发服务

neu命令行工具提供了内置特性来启用HMR，通过修补根html文件来实现

修改配置文件来激活热重载

```json
// react项目设置
"cli": {
    // --- other options
    "frontendLibrary": {
        // 1.告诉neu命令行工具根html文件和开发服务的地址
        "patchFile": "/react-src/public/index.html",
        "devUrl": "http://localhost:3000",
        // 2.添加特定前端库开发命令
        "projectPath": "/react-src/",
        "initCommand": "npm install",
        "devCommand": "BROWSER=none npm start",
        "buildCommand": "npm run build"
    }
}

// vue项目设置
"cli": {
    // --- other options
    "frontendLibrary": {
        // 1.告诉neu命令行工具根html文件和开发服务的地址
        "patchFile": "/vue-project/index.html",
        "devUrl": "http://localhost:5173",
        // 2.添加特定前端库开发命令
        "projectPath": "/vue-project/",
        "initCommand": "npm install",
        "devCommand": "BROWSER=none npm run dev",
        "buildCommand": "npm run build"
    }
}
```

最后，通过如下命令运行Neutralinojs应用

```sh
# 执行devCommand
# 以开发模式启动react开发服务并运行Neutralinojs应用
neu run
```

#### 打包

```sh
# 首先会执行buildCommand
# 因此应用程序包会使用当前react项目的源代码
neu build --release
```

### 本地接口

为了通过Neutralinojs服务，与本地操作进行交互，Neutralinojs为开发人员提供了一个JavaScript客户端库（即 Neutralino.js，一下称客户端库）

Neutralinojs的JavaScript客户端实现在Neutralino.js文件中

这是Neutralinojs项目中必须有这个客户端库的原因

客户端库将其JavaScript接口暴露给浏览器的window作用域

你可以用普通js，通过Neutralino或window.Neutralino访问这些接口

如果你使用了前端框架来构建你的应用前端，可以从`@neutralinojs/lib`这个npm模块导入Neutralinojs命名空间来使用这些接口

```js
import { app } from '@neutralinojs/lib';

const conf = await app.getConfig();  // Vanilla Js: await Neutralino.app.getConfig()
```

- 假设你需要从操作系统获取一个环境变量值，本地接口工作流程为
    - 调用 Neutralino.os.getEnv这个js方法
    - 客户端库发送WebSocket信息给Neutralinojs服务
    - Neutralinojs服务执行本地操作，获取给定的环境变量值
    - Neutralinojs服务发送包含该环境变量值的WebSocket信息给客户端库
    - 客户端库解析从服务器得到的promise，最终得到结果

客户端库维护一个任务池，来映射Neutralinojs服务的消息，通过请求的uuid来进行匹配

Neutralinojs所有模式使用都这种通信机制，为开发人员提供了许多本地操作

在Neutralinojs应用配置文件中，可以使用 nativeAllowList和nativeBlockList配置允许的和不允许的本地接口

```json
{
  "nativeAllowList": [
    "app.*",
    "window.*",
    "os.execCommand"
  ],
  "nativeBlockList": [
    "filesystem.remove",
    "extensions.*"
  ]
}
```

### 发布应用

构建的时候会生成各个平台特定的二进制文件+资源文件resources.neu

打包的时候只需包含平台特定的二进制文件和resources.neu就可以了

如为linux_x64平台打包，则只需包含myapp-linux_x64和resources.neu这两个文件就行了

```sh
# 生成各个平台特定的二进制文件+资源文件resources.neu
# --release：将"neu build"生成的dist目录下以应用名为名称的整个文件夹，打包为"应用名-release.zip"文件,然后放到dist目录下
# 笔者认为还不如不加这个参数
neu build [--release]

# 生成各个平台特定的二进制文件（内置了资源文件）
neu build --embed-resources
```

内置资源文件的方式使得每次更新应用的资源文件必须重新构建

如果你想在各个发布版本之间独立更新资源文件，而不重新执行构建

使用标准的平台特定的二进制文件和resources.neu这种方式会更合适

此外还可以使用hschneider/neutralino-build-scripts这个社区项目打包安装脚本，具体请参考Neutralinojs官网教程

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
