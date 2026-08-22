# AI Note

深度学习是机器学习的一个分支

神经网络的核心是“微积分”（梯度计算）

神经网络训练的本质是反向传播算法，这需要计算损失函数对模型参数的偏导数（即梯度）

Hugging Face：官网<https://huggingface.co/>， 托管了海量的模型、数据集和演示应用，是全球通用的开源 AI 社区，是AI界的“github”

HF-Mirror：官网 <https://hf-mirror.com>，Hugging Face的镜像

ModelScope：官网<https://www.modelscope.cn>，是国内版的Hugging Face

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

## AI桌面端软件使用心得

- Jan：<https://github.com/janhq/jan>
    - 文件小，启动速度快，但是不能输入中文，看以后怎么样了

- CherryStudio：<https://github.com/CherryHQ/cherry-studio>
    - 文件比Jan大，启动还慢，但是可以输入中文，模型供应商比较多且不能隐藏，2.0版本开始不能删除聊天，先用着吧

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

# 先设置镜像源，以防HuggingFace下载很慢export HF_ENDPOINT="https://hf-mirror.com"


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
uv run tts --text "你好，我是爱坤,人称鸡哥，喂我花生！" --model_name "tts_models/zh-CN/baker/tacotron2-DDC-GST" --out_path ~/Downloads/output.wav

# tts_models/en/multi-dataset/tortoise-v2：这个模型很大并且生成语音文件要好久，不推荐使用
# tts_models/en/ljspeech/glow-tts：生成速度块并且不会漏字，推荐
uv run tts --text "hello,world! I'm kunJack!" --model_name "tts_models/en/ljspeech/glow-tts" --out_path ~/Downloads/output.wav

# tts_models/multilingual/multi-dataset/your_tts可以将自己的声音作为输入，例子暂时不列出来，目前没有那个需求，不支持中文转语音
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
## llama.cpp

官网：https://llama.app/

llama.cpp是本地部署ai模型的命令行工具



### 安装llama.cpp

arch教程官网：https://wiki.archlinux.org/title/Llama.cpp

- 对于llama.cpp
    - Dense 模型 → Vulkan 更合适：每次推理需完整扫过全部权重，瓶颈在内存带宽。
        - Vulkan 驱动开销低、带宽利用率高，在消费级 AMD 硬件上表现更稳定。
    - MoE 模型 → HIP 更合适：每次只激活一小部分专家，权重搬运量大幅减少，瓶颈转移到稀疏矩阵计算效率。

- 笔者用Ornith-1.5-9B-Q4_K_M.gguf模型测试llama.cpp(ggml-hip)和ollama(ollama-vulkan)
    - 上下文：32768
    - 显卡：RX9070
    - 结果是使用的显存存6.7G左右，差别不大
    - 生成响应词元的速度，llama.cpp（68t/s左右），ollama（58t/s左右），前者快了大概10个词元每秒

```sh
# ggml-vulkan和ggml-hip用于显卡加速
# ggml-vulkan一般更节省内存，但是生成的词元速度比较慢
# ggml-hip的软件包一般也更大，也更花内存，但是生成的词元速度更快
# 根据自己的硬件条件进行选择，只安装一个就行了
sudo pacman -S llama-cpp  ggml-cpu ggml-hip
```

### 拉取模型

```sh
# 从Hugging Face拉取模型
# 也可以自己从网上下载gguf模型然后运行
llama-cli -hf org/model
```

### 运行

```sh
# 命令行交互方式运行
# -c 32768，指定上下文长度，越大越吃显存
# -ctk q8_0，Key 张量：负责计算注意力权重（即"模型关注哪些 token"），相当于模型的"注意力分配器"
# Key 对量化极其敏感，激进量化会明显降低输出质量
# -ctv q8_0，Value 张量：负责提供实际的内容信息（即"模型从关注的 token 中提取什么"），相当于"信息载体"
# Value 容忍度更高，激进量化仍有降级风险但相对安全
# -ngl 要卸载到GPU的层数，可以是auto、all或一个整数，显存不够就调小
# -cmoe，将所有专家权重保留在内存中
# --n-cpu-moe， 设置专家权重保留在内存中的数量
llama-cli -m model.gguf -c 32768

# 以API服务器运行，自带WebUI
# 也可以使用cherry-studio这种桌面端软件进行链接，添加ai提供商，然后用openai的api，指定地址端口就行了
# --no-webui，禁用WebUI，只提供API服务
llama-server -m Ornith-1.5-35B-Q4_K_M.gguf -ngl all --n-cpu-moe 18 -ctv q8_0 -c 8192 --temp 0.2 --top-p 0.2 --host 0.0.0.0 --port 8888 --no-webui
```

## Ollama

Ollama是一个类似docker的用于本地快速部署模型进行推理的工具，很多命令都跟docker相似

Ollama只支持部分模型的thinking展示和折叠

量化就是将模型的高精度参数进行压缩，以减小模型体积、降低内存占用并提升推理速度，代价是可能会有微小的精度损失

|精度与量化后缀|描述|
|:-|:-|
|BF16|Brain Float 16，专为快速计算设计的高精度格式，具有与 FP32 相似的动态范围，但内存占用更少，训练更稳定。适合硬件支持 BF16 加速且追求高质量输出的场景|
|FP16|标准16位浮点数。平衡了精度和速度，是大部分设备推理的首选|
|FP8/Q8_0|8位浮点数或整数量化。精度极高，几乎无损（接近原始模型），但体积较大|
|Q4_K_M/Q4_0|4位整数量化。这是默认推荐的“黄金平衡”版本，能在几乎察觉不到质量下降的情况下，将模型体积压缩到原来的四分之一左右，推理速度快|
|Q3_K_S/Q2_K|3位/2位极低比特量化。体积最小，速度最快，但智力（精度）损失相对明显，适合内存极小或性能极弱的设备尝鲜|

|参数规模后缀|描述|
|:-|:-|
|数字 + B|如27b，B 代表 Billion（十亿），表示模型的参数量。数字越大，模型越聪明，但对硬件（显存/内存）的要求也越高。|
|文字标识|如 tiny（极小）、small（较小）、large（较大），直观反映模型的体量|

|功能与定位后缀|描述|
|:-|:-|
|Instruct|经过指令微调，擅长理解并执行明确的任务（如“写一封邮件”、“总结文档”）|
|Chat|专注于对话场景，训练数据包含大量多轮对话，口语化表达和上下文连贯性更好|
|Base|：基础版模型，通常未经过指令微调，更适合用于二次开发或继续训练|
|特定任务|如 Code（针对代码生成优化）、Vision（支持看懂图片的多模态能力）|

|版本与训练标识|描述|
|:-|:-|
|版本号 (v0.1, v2)|代表模型的迭代。新版本通常修复了逻辑错误、增强了长文本理解或减少了幻觉输出|
|训练量标注|如 4e1t，表示模型在 1万亿 Token 的数据集上完整训练了 4 个 Epoch，数值越高说明对数据的“消化”越充分|

|框架标识后缀|描述|
|:-|:-|
|MLX|苹果公司专为自家 Apple Silicon 芯片（M1/M2/M3/M5系列）开发的开源机器学习框架|
|MTP (Multi-Token Prediction, 多令牌预测)|一种推测解码（Speculative Decoding）的进阶技术。传统的推理是“一个字一个字”地预测，而 MTP 允许模型在一次前向传播中“一口气并行预测多个后续 Token”，然后通过验证机制确认有效性|
|MXFP8 (Microscaling FP8, 微缩放 8位浮点)|一种 8位浮点数计算精度技术，属于 MX（Microscaling）规范的一部分。它的核心设计是“分块共享指数”，即每 32 个元素共享一个 8位指数，每个元素仅保留 4位数据|
|NVFP4 (NVIDIA 4位浮点格式)|NVIDIA 为其 Blackwell GPU 架构引入的一种创新的 4位浮点量化格式。它采用 E2M1 编码（1个符号位、2个指数位、1个尾数位），并采用了更精细的“双层缩放策略”（微块缩放+张量级缩放）|
|A3B|3B Active Parameters（30亿活跃参数），它通常与 MoE（Mixture of Experts，混合专家）架构绑定出现，例如 30B-A3B，表示该模型在进行每一次推理计算时，实际激活并参与运算的参数只有 30 亿个，这通常是一个性价比极高的选择——它能在普通个人电脑（如单张 4090 显卡或 16GB/32GB 内存的 Mac）上，以极低的资源消耗提供接近大模型的聪明才智和极快的响应速度|
|E2B|代表的是 Effective 2B（有效 2B 参数），虽然模型的实际物理参数量可能更大，但通过创新的架构设计，它在运行时只需要消耗相当于 2B（20亿）参数模型的内存和算力|
|it|Instruction-Tuned（指令微调版）,代表该模型经过了专门的人类指令跟随训练和多轮对话对齐,开箱即用，能精准听懂你的自然语言指令，非常适合用于日常对话、问答、代码生成和内容创作。对于普通用户来说，认准带 -it 的版本准没错|
|qat|Quantization-Aware Training（量化感知训练）,这是一种在模型训练阶段就提前模拟量化误差的先进技术,4-bit 量化下的质量损失被控制在 1% 以内，几乎感觉不到智商下降,大幅降低了内存和显存需求。例如 Gemma 4 的 E2B 模型采用 QAT 后，内存占用甚至能降至 1GB 以下，让老旧设备也能流畅运行，请毫不犹豫地选择 QAT 版本|

```sh
# ArchLinux安装Ollama
sudo pacman -S ollama

# GPU加速
# 如果是amd显卡，有两种gpu加速方法，要注意的是ollama-vulkan和ollama-rocm不要同时安装
# rocm加速：安装rocm-hip-sdk和ollama-rocm
# vulkan加速：安装ollama-vulkan，据说比rocm更快
# 笔者自己的测试结果是ollama-rocm软件包非常大，并且占用内存、推理速度都不如ollama-vulkan
# 安装后记得重启电脑
sudo pacman -S rocm-hip-sdk ollama-rocm
sudo pacman -S ollama-vulkan

# 如果想要修改ollama模型的保存位置
# 如果是通过systemd启动，要通过sudo systemctl edit ollama 设置环境变量
export OLLAMA_MODELS=/pato/to/model

# 设置镜像源
export OLLAMA_BASE_URL=https://mirrors.tuna.tsinghua.edu.cn/ollama

# 要先启动ollama服务，才能使用后续的各种命令
# 也可以通过systemd启动并设置开机启动：sudo systemctl enable --now ollama
# 使用systemd启动ollama的话，模型默认路径是：/var/lib/ollama/blobs
# http://localhost:11434
ollama serve

# 可以在拉取模型的时候指定镜像源
[OLLAMA_BASE_URL=https://mirrors.tuna.tsinghua.edu.cn/ollama] ollama pull 模型名称:标签


# gemma4:e4b-it-qat，使用更少的内存(5G左右)，适合日常问答、处理文档、轻度多模态任务等边缘计算场景
# gemma4:12b 用了RX9070的9G左右显存，回答比网页版的文心一言稍慢
# gemma4:12b-it-qat，适合需要复杂推理、代码生成、Agent 工作流以及高质量多模态理解的用户
# gemma4:26b-a4b-it-qat回答确实快，但是26b已经把RX9070的16G显存用完，只使用交互终端问答没感觉到卡顿，但是切换到别的应用就明显卡顿了
# 这么算下来的话qwen3.6:35b-a3b-q4_K_M是没办法玩了
# 可以下这两个玩玩
ollama pull gemma4:e4b-it-qat
ollama pull gemma4:12b-it-qat

# 7G多显存，更推荐玩这个
ollama pull qwen3.5:9b-q4_K_M

# 12G多显存
ollama pull deepseek-v2:16b-lite-chat-q4_K_M

# 查看已经下载的模型列表
ollama list

# # 查看正在运行的模型列表
ollama ps

# 运行模型
# 如果进入了交互式聊天对话框，输入/bye停止会话
# --verbose，在每个对话最后输出词元生成速度
ollama run  模型名称:标签 --verbose

# 停止运行的模型
# 即使在终端停止了模型，只要用桌面客户端应用连接了ollama，发送消息就会唤醒或启动模型
ollama stop 模型名称:标签
```

### 拉取GGUF格式的模型

ollama还可以拉取Hugging Face/魔塔社区的GGUF格式的模型在本地运行

Hugging Face官网地址：<https://huggingface.co/models?library=gguf&sort=trending>

魔塔社区官网地址：<https://www.modelscope.cn/models>

```sh
# 拉取Hugging Face的模型
ollama pull hf.co/<username>/<model-repository>
# 例
ollama pull hf.co/AtomicChat/Qwen3.6-27B-DFlash-GGUF


# 拉取魔塔社区的模型
ollama run modelscope.cn/<username>/<model-repository>
# 例
ollama run modelscope.cn/hf/antirez-deepseek-v4-gguf
```

### Modelfile

ollama拉取模型的时候没有断点续传的功能

如果网络卡的话可能还会出现下载进度倒退的情况

对于从huggingface下载的gguf模型可以自己从网站上面下载

好然后创建Modelfile，通过命令手动导入ollama，最后运行

反正这也可以作为一个后备方案了

```sh
FROM /path/to/file.gguf

PARAMETER temperature 0.2

PARAMETER num_ctx 32768
```

- 添加Modelfile模型

```sh
ollama create choose-a-model-name -f <location of the file e.g. ./Modelfile>

ollama run choose-a-model-name
```

### 修改上下文的词元长度

ollama默认上下文长度是 4096 tokens

- 在启动ollama服务时设置

```sh
# 通过环境变量设置上下文长度
OLLAMA_CONTEXT_LENGTH=8192 ollama serve
```

- 通过/set设置，在会话中直接输入下面的命令

```sh
/set parameter num_ctx 4096
```

- api调用时设置

```sh
curl http://localhost:11434/api/generate -d '{
    "model": "llama3.2",
    "keep_alive": 30m,
    "prompt": "Why is the sky blue?",
    "options": {
        "num_ctx": 4096
    }
}'
```

### 禁用Ollama云服务

- 方法1：通过设置环境变量OLLAMA_NO_CLOUD=1

- 方法2: 创建文件~/.ollama/server.json，输入如下内容
    - 如果是systemd服务启动的则是/var/lib/ollama/.ollama/server.json，并且还要将文件属主和所属组都设置为ollama

```json
{
  "disable_ollama_cloud": true
}
```

设置好后重启ollama服务，查看日志会看到：Ollama cloud disabled: true

## AI训练/预测流程

- 模型训练流程
    - 准备数据集
    - 构建神经网络
    - 构建模型（让这个模型应用上一步的神经网络）
    - 训练配置（如何训练，训练数据集、验证数据集、测试数据集）
    - 训练模型
    - 保存模型
- 模型使用流程
    - 加载模型（上面保存的模型）
    - 预测（给模型一个新输入，让其判断）

- 未训练的模型是参数值不确定的公式，训练好的模型是参数值确定的公式

![机器学习算法](/images/机器学习算法.png)

## PyTorch

官网：<https://pytorch.org/>

PyTorch是一个AI框架，主打动态图计算，跟TensorFllow类似，但是后者是静态图计算的

- 下载：<https://pytorch.org/get-started/locally/>
    - 选择pytorch版本、系统、包管理器、语言、根据显卡选择计算平台最后得到安装命令

```sh
# 根据情况将pip3改成pip
pip3 install torch torchvision --index-url https://download.pytorch.org/whl/rocm7.2

# 如果安装了uv，则添加前缀uv
# 下载太慢了，笔者试了一下用国内镜像源（摇头），最后还是用魔法下载完的
uv pip install torch torchvision --index-url https://download.pytorch.org/whl/rocm7.2
```

- 测试

```py
import torch

# 创建一个5行3列的随机张量
x = torch.rand(5, 3)

# 用当前可用的GPU加速计算
if torch.accelerator.is_available():
    tensor = tensor.to(torch.accelerator.current_accelerator())

print(x)
```

- GPU加速通用的输出

```py
# 英伟达和AMD都是用torch.cuda.is_available()这个语句检查检查显卡加速是否可用
device = torch.accelerator.current_accelerator().type if torch.accelerator.is_available() else "cpu"

print(f"Using {device} device")

if device != "cpu":
    print("gpu type:", torch.cuda.get_device_name(0))
```

- 模型使用GPU加速

```py
device = torch.accelerator.current_accelerator().type if torch.accelerator.is_available() else "cpu"
print(f"Using {device} device")

# Define model
class NeuralNetwork(nn.Module):
    def __init__(self):
        super().__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10)
        )

    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits

model = NeuralNetwork().to(device)
print(model)
```

### tensor

```py
import torch

# 定义一个形状（元组）作为张量的维度
shape = (2, 3)

# 用随机值填充指定形状，来创建张量
randTensor = torch.rand(shape)

# 用全1填充指定形状，来创建张量
onesTensor = torch.ones(shape)

# 用全0填充指定形状，来创建张量
zerosTensor = torch.zeros(shape)

print(f"随机张量:\n{randTensor}\n")
print(f"全1张量:\n{onesTensor}\n")
print(f"全0张量:\n{zerosTensor}\n")

# 张量的属性
tensor = torch.rand(3, 4)

print(f"Shape of tensor: {tensor.shape}")
print(f"Data type of tensor: {tensor.dtype}")
print(f"Device tensor is stored on: {tensor.device}")

# 张量的操作：索引和切片
tensor = torch.ones(4, 4)
print(f"First row: {tensor[0]}")
print(f"First column: {tensor[:, 0]}")
print(f"Last column: {tensor[..., -1]}")
# 将第二列的值设置为0
tensor[:, 1] = 0
print(tensor)

# 张量的操作：连接
# 将张量沿着列的维度进行拼接
t1 = torch.cat([tensor, tensor, tensor], dim=1)
print(t1)

# 张量的操作：矩阵乘法
# @ 运算符表示矩阵乘法
# tensor.T是得到tensor的转置，原来是(m, n)，转置后是(n, m)
# matmul方法和@的功能一样，都是进行矩阵乘法运算
# 下面的y1,y2,y3的结果是一样的，都是tensor与tensor转置的矩阵乘积
y1 = tensor @ tensor.T
y2 = tensor.matmul(tensor.T)

# 创建一个与y1形状相同的张量y3，并初始化为[0,1)随机值
y3 = torch.rand_like(y1)
# 使用torch.matmul进行矩阵乘法运算，并将结果存储在y3中
torch.matmul(tensor, tensor.T, out=y3)
print(y1)

# 张量的操作：逐元素乘法
# 逐元素乘法（Element-wise Multiplication），也常被称为哈达玛积（Hadamard Product）
# 将两个张量在相同位置上的元素分别相乘，要求参与运算的两个张量形状完全相同
# 下面的z1,z2和z3的结果都是一样的，都是一个4x4的张量
z1 = tensor * tensor
z2 = tensor.mul(tensor)
z3 = torch.rand_like(tensor)
torch.mul(tensor, tensor, out=z3)
print(z1)

# 张量的操作：合计元素值
# 合计张量所有元素的值到一个单元素张量中
agg = tensor.sum()
# 将单元素张量转换为Python数值
agg_item = agg.item()
print(agg, type(agg))
print(agg_item, type(agg_item))

# 张量的操作：就地操作
# 就地操作的操作结果会覆盖操作数，它们一般有下划线后缀，如x.copy_(y), x.t_(), 将会改变x的值
# 就地操作能节省内存，但在计算衍生品时，由于历史的直接损失，可能会出现问题。因此，不鼓励使用它们。
print(f"{tensor} \n")
# 每个元素值都加5
tensor.add_(5)
print(tensor)

# torch的tensor是可以和NumPy互相转换的，官网也提供了一些例子，笔者目前用不到就先不记录笔记了
```

### 数据集和数据加载器

pytorch提供了两个数据基本组件：`torch.utils.data.DataLoader` 和 `torch.utils.data.Dataset`，允许你使用预加载的数据集和你自己的数据

Dataset保存了样本及对应的标签

DataLoader在数据集周围包装了一个可迭代对象，以方便地访问样本

pytorch域库提供了许多预加载的数据集（如FashionMNIST）

这些数据集是`torch.utils.data.Dataset`的子类，并实现了特定数据的特定函数，它们可用于原型化和基准化模型

顺便一提，MNIST（Modified National Institute of Standards and Technology）是计算机视觉和机器学习领域中最经典、最基础的手写数字图像数据集

它通常被用作入门深度学习、测试新算法或进行基准测试的“Hello World”级数据集

MNIST 是由 Yann LeCun、Corinna Cortes 和 Christopher J.C. Burges 等人在 1998 年发布的。它是基于美国国家标准与技术研究院（NIST）的原始数据集（NIST Special Database 1 和 3）修改而来的，旨在提供一个标准化的、易于处理的基准，以便研究人员能够专注于算法本身，而不是数据预处理

#### FashionMNIST数据集

FashionMNIST数据集包含了1-10种类别的6万训练样本和1万测试样本的28x28大小的灰度图像和对应的标签

- FashionMNIST数据集有以下参数
    - root， train/test 数据存放的路径
    - train，指定是训练还是测试数据集
    - download=True，如果root路径不可用，则从Internet下载数据
    - transform 和 target_transform，指定特征和标签转换

##### 加载数据集

```py
import torch
from torch.utils.data import Dataset
from torchvision import datasets
from torchvision.transforms import v2

# 需要先安装依赖包：uv pip install matplotlib
import matplotlib.pyplot as plt

tranning_data = datasets.FashionMNIST(
    root = "data",
    train = True,
    download= True,
    # Compose 是一个“组合器”，它的作用是将列表中的多个变换（transforms）打包在一起，并按照列表中的顺序依次执行
    # 第一步，v2.ToImage()，将输入的图像统一转换为 PyTorch 的 tv_tensors.Image 类型
    # 第二步，v2.ToDtype(torch.float32, scale=True)，转换图像张量的数据类型
    # torch.float32，将像素值的数据类型转换为 32位浮点数
    # scale=True，这是关键参数。当设置为 True 时，它会自动将像素值从整数范围 [0, 255] 线性缩放（归一化）到浮点数范围 [0.0, 1.0]
    # 也就是说将原始的整数像素值除以255.0
    transform= v2.Compose([v2.ToImage(), v2.ToDtype(torch.float32, scale=True)])
)

test_data = datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=v2.Compose([v2.ToImage(), v2.ToDtype(torch.float32, scale=True)])
)
```

##### 迭代和可视化数据集

我们可以用training_data[index]，像列表一样手动索引数据集

我们使用matplotlib来可视化训练数据中的一些样本

```py
label_map = {
    # T恤
    0: "T-Shirt",
    # 裤子
    1: "Trouser",
    # 套衫
    2: "Pullover",
    # 裙子
    3: "Dress",
    # 外套
    4: "Coat",
    # 凉鞋
    5: "Sandal",
    # 衬衫
    6: "Shirt",
    # 运动鞋
    7: "Sneaker",
    # 包
    8: "Bag",
    # 踝靴：一种只到踝部的靴子，通常用于保护踝关节
    9: "Ankle Boot",
}

# 创建一个新的图表窗口（画布），尺寸是(8, 8)，英寸，一边后续在上面画图
figure = plt.figure(figsize=(8, 8))

cols, rows = 3, 3

for i in range(1, cols * rows + 1):
    # 从训练数据集中随机抽取一个样本的索引，并将其转换为普通的Python整数
    # len(tranning_data)，训练集长度
    # size=(1, ) 表示生成一个只包含 1 个元素的一维张量
    # .item()，将这个张量转换为普通的Python整数
    sample_idx = torch.randint(len(tranning_data), size=(1, )).item()
    # 通过刚刚获取的随机索引，从训练数据集中提取出对应的图像和标签(分类数字，如：0)
    img, label = tranning_data[sample_idx]
    # 在画布上划分网格并添加一个子图（Subplot）
    # rows, cols，指定画布分成几行几列，最终画布被分成rows × cols 的网格
    # i，指定要在这个网格的第几个位置画图从左上角开始，从左到右、从上到下依次编号，从 1 开始
    figure.add_subplot(rows, cols, i)
    # label_map[label]，通过分类数字获取分类字符串
    plt.title(label_map[label])
    print(f"label: {label}, label_map[label]: {label_map[label]}")
    # 关闭坐标轴的所有显示元素
    plt.axis("off")
    # imshow只是把图像画好，但不弹窗显示
    # img.squeeze()，去除所有大小为 1 的维度
    # tranning_data如果包含64张图像，则为[64, 1, 28, 28]
    # 那么img就是[1, 28, 28]
    # 单张灰度图：[1, 28, 28]，单张彩色图：[3, 28, 28]
    # plt.imshow() 在显示单张图像时，通常只接受 2 维数组 (高, 宽) 
    # 多出来的维度，将导致imshow报错或异常，使用squeeze后就会被“压缩”成标准的 (28, 28)，从而可以顺利显示
    # cmap，colormap，色彩映射的缩写，以灰度显示图像
    plt.imshow(img.squeeze(), cmap="gray")

# 弹窗显示图表
plt.show()
```

#### 给你的文件创建自定义数据集

一个自定义Dataset类必须实现三个方法： `__init__`, `__len__`, 和 `__getitem__`

FashionMNIST图像保存在img_dir目录下

标签保存在独立的csv文件annotations_file中，内容类似下面这样

```csv
tshirt1.jpg, 0
tshirt2.jpg, 0
......
ankleboot999.jpg, 9
```

```py
import os

# 需要安装依赖包: uv pip install pandas
import pandas as pd

from torch.utils.data import Dataset
from torchvision.io import decode_image

class CustomImageDataset(Dataset):
    # __init__方法只在实例化Dataset对象时运行一次
    # 初始化包含图像的目录、csv标签文件和两个转换器
    def __init__(self, annotations_file, img_dir, transform=None, target_transform=None):
        # 读取csv标签文件内容
        self.img_labels = pd.read_csv(annotations_file)
        self.img_dir = img_dir
        self.transform = transform
        self.target_transform = target_transform

    def __len__(self):
        """返回数据集的样本数大小"""
        return len(self.img_labels)

    # 根据所给出的索引参数，从数据集加载并返回一个样本
    def __getitem__(self, idx):
        # 拼接指定索引idx所在图像文件的完整路径
        # self.img_dir，图像所在目录
        # self.img_labels.iloc[idx, 0]，图像文件名
        # self.img_labels存的数据类似这样："tshirt1.jpg, 0"
        # iloc[idx, 0]，idx是"tshirt1.jpg, 0"的索引，0是图像文件名"tshirt1.jpg"的索引，1是标签"0"的索引
        img_path = os.path.join(self.img_dir, self.img_labels.iloc[idx, 0])

        # 从指定路径加载图像文件并转换为tensor
        image = decode_image(img_path)

        # 获取图像文件相应的标签
        label = self.img_labels.iloc[idx, 1]
        # 如果定义了转换器就调用它们
        if self.transform:
            image = self.transform(image)
        if self.target_transform:
            label = self.target_transform(label)
        # 返回一个包含张量图像和相应标签的元组
        return image, label
```

#### 数据加载器

数据加载器是一个可迭代对象，它用一个简单的API，把复杂的数据集遍历做了抽象

Dataset每次检索数据集中一个样本的特征和标签

真正训练一个模型的时候，通常以小批量的方式来传递样本

重新洗牌（reshuffle）是对整个数据集进行的，而不是对某个小批量里面的数据进行的

在每轮遍历数据集前，都先重新洗牌（reshuffle）整个数据集以减少模型过拟合

并使用Python的多线程来加速数据检索

- 例如训练集有1万样本，训练5轮
    - 首先
        - 按这1万样本的原本的顺序，比如说得到一个list1
        - 然后对list1按顺序分成64个样本一批，逐批送入模型，直到将这1万样本都训练完就是1轮（1个epoch）
    - 然后
        - 第二轮，因为shuffle=True，进行重新洗牌，将这1万样本的顺序打乱，得到一个新的列表list2
        - 然后对list2按顺序分成64个样本一批，逐批送入模型，直到将这1万样本都训练完
    - 直到训练完5轮

```py
from torch.utils.data import DataLoader

# batch_size，每次小批量传递64个样本
# shuffle=True，每一轮都重新洗牌数据集
train_dataloader = DataLoader(training_data, batch_size=64, shuffle=True)
test_dataloader = DataLoader(test_data, batch_size=64, shuffle=True)
```

##### 通过数据加载器迭代数据集

上一步我们已经将数据集加载到DataLoader，可以根据需要迭代数据集了

```py
# 下面的每一次迭代都从数据集返回一批train_features和train_labels（包含batch_size=64个特征和标签）
# 从数据加载器中提取出第一个批次的数据
# iter(train_dataloader)：把加载器变成可迭代对象，iter() 是 Python 的内置函数，它的作用是把一个对象转换成“迭代器（Iterator）”
# next(...)：提取下一个数据块，next() 也是 Python 的内置函数，作用是让迭代器向前走一步，并返回当前这一步的数据
# train_features：包含64张图片的像素数据，[64, 1, 28, 28]，分别表示64张图像，单通道（灰度图像），图像高度，图像宽度
# train_labels：包含64张图片对应的标签数据，这里是一个list
train_features, train_labels = next(iter(train_dataloader))
print(f"Feature batch shape: {train_features.size()}")
print(f"Labels batch shape: {train_labels.size()}")
# train_features[0]得到[1, 28, 28]
# squeeze()去掉所有大小为1的维度，得到[28, 28]
img = train_features[0].squeeze()
label = train_labels[0]

plt.imshow(img, cmap="gray")
plt.show()
print(f"Label: {label}")
```

### 转换器

数据并不总是以训练机器学习算法所需的最终处理形式出现

我们使用转换器对数据进行一些操作，使其适合于训练

- 所有的TorchVision数据集有两个参数，它们接受包含转换逻辑的可调用对象
    - transform，用来修改特征
    - target_transform，用来修改标签

`torchvision.transforms`模块提供了几个常用的开箱即用的转换器

FashionMNIST特征是PIL图像格式的，标签是整数的

为了将其用于训练，需要将特征转换为标准化张量，将标签转换为`one-hot`编码张量

为了进行这些转换，我们使用`torchvision.transforms.v2`API以及`torch.nn.functional.one_hot`API

```py
import torch
import torch.nn.functional as F
from torchvision import datasets
from torchvision.transforms import v2

ds = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    # v2.ToImage()：将PIL图像或NumPy n维数组转为torchvision.tv_tensors.Image张量
    # v2.ToDtype：将像素灰度值转为浮点数，scale=True，将像素灰度值转为 [0., 1.]范围的浮点数
    transform=v2.Compose([v2.ToImage(), v2.ToDtype(torch.float32, scale=True)]),
    
    target_transform=v2.Lambda(
        # y代表从数据集里取出的原始标签
        # F.one_hot：将原本的整型张量转换成one-hot编码的整型张量
        # torch.tensor(y)：将y转成张量
        # num_classes=10，指定了张量的长度为 10（0~9 共 10 个类别，对应数据集的标签数），如3转换后为[0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
        # float()：F.one_hot默认生成的是整数类型的张量，需要转为浮点数来匹配预期数据类型
        # 在深度学习计算（如计算交叉熵损失、矩阵乘法）时，通常需要转为浮点数
        lambda y: F.one_hot(torch.tensor(y), num_classes=10).float()
    )
)
```

### 构建神经网络

神经网络由对数据执行操作的层/模块组成

`torch.nn`命名空间提供了构建你自己的神经网络所需的所有构建块

PyTorch中的每个模块都是`nn.Module`的子类

神经网络本身就是一个由其他层/模块组成的模块

这种嵌套结构允许你轻松地构建和管理复杂的体系结构

```py
import torch
from torch import nn

# 如果当前有加速器可用，我们希望用加速器训练我们的模型，否则还是用CPU
device = torch.accelerator.current_accelerator().type if torch.accelerator.is_available() else "cpu"

print(f"Using {device} device")

# 通过派生nn.Module的子类来定义我们自己的神经网络
class CustomImageNeuralNetwork(nn.Module):
    # 在构造方法中初始化神经网络层
    def __init__(self):
        super().__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10),
        )

    # 实现forward方法来操作输入数据
    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits
    
# 创建神经网络实例，并将移动到指定设备
model = CustomImageNeuralNetwork().to(device)

# 打印神经网络的结构
print(model)


# 用指定设备创建一个只有1张图像样本的输入张量，作为模型入参
X = torch.rand(1, 28, 28, device=device)

# 通过传递输入数据来使用这个模型，会自动执行模型的forward方法，以及一些后台操作，千万不要直接调用forward方法！
# 模型将返回一个2维的张量作为结果（形状是[1,10]），dim=0的值批处理大小，这里是1
# dim=1的值是每一个分类对应的10个原始预测值，它们有负数，且加起来不等于1
logits = model(X)


# 创建nn.Softmax模块的实例
# dim=1，在dim=1上进行计算，这里对应就是logits的dim=1维度
softmax = nn.Softmax(dim=1)

# 通过将输出传递给nn.Softmax模块的实例来获得预测概率
# 如果在一个对象后面加上()，Python 会自动去寻找并执行这个对象内部的 __call__() 方法
pred_probab = softmax(logits)

# argmax：argument of the maximum，最大值所在的索引
# 1表示dim=1
y_pred = pred_probab.argmax(1)
rawpv = logits[0, y_pred]
print(f"logits : {logits}")
print(f"Predicted class: {y_pred}, raw predicted values: {rawpv}")

# 创建一个只有3张图像样本的输入张量
input_image = torch.rand(3, 28, 28)
# [3, 28, 28]
print(input_image.size())

# nn.Flatten()：扁平化，将2维的28x28图像转为包含784个连续像素值的数组，dim=0维度保持不变
fltten = nn.Flatten()
flat_image = fltten(input_image)
# [3, 784]
print(flat_image.size())

# 线性层是一个使用其存储的权重和偏置对输入应用线性变换的模块
# 也就是把输入特征映射到一个新的特征空间中
# 在训练过程中，模型会通过反向传播不断调整权重和偏置的值，直到找到最优的加工方式
print(f"Before Linear: {flat_image}\n\n")
layer1 = nn.Linear(in_features=28*28, out_features=20)
hidden1 = layer1(flat_image)
print(hidden1.size())

# nn.ReLU（Rectified Linear Unit，修正线性单元）的核心作用确实就是把输入中小于 0 的数全部变成 0，大于等于 0 的数保持不变
# 数学公式：f(x)=max(0,x)
# 在神经网络中，nn.Linear 只是做线性变换（矩阵乘法加偏置），如果一层层只堆叠线性层，无论多少层，最终都等价于一个单层线性模型
# 加上 nn.ReLU 后，就引入了非线性。这就好比给模型增加了“拐弯”的能力，让它能够去拟合现实世界中复杂的、非线性的规律
# 非线性激活会在模型的输入和输出之间创建复杂的映射
# 它们应用于线性变换后引入非线性，帮助神经网络学习各种各样的现象
print(f"Before ReLU: {hidden1}\n\n")
hidden1 = nn.ReLU()(hidden1)
print(f"After ReLU: {hidden1}")

# nn.Sequential是一个有序的模块容器，数据按照定义的顺序在所有模块中传递
# 您可以使用它来组成一个快速网络，如seq_modules
seq_modules = nn.Sequential(
    fltten,
    layer1,
    nn.ReLU(),
    nn.Linear(20, 10)
)

input_image = torch.rand(3, 28, 28)
logits = seq_modules(input_image)

# nn.Softmax
# 神经网络的最后一个线性层返回logits：[-infty， infty]中的原始值
# 这些值被传递给nn.Softmax模块
# logits会被缩放到值[0,1]，表示模型对每个分类的预测概率
# dim参数表示值之和必须为1的维度
softmax = nn.Softmax(dim=1)
pred_probab = softmax(logits)

# 模型参数
# 神经网络中的许多层都是参数化的，例如，在训练过程中与优化相关的权重和偏差
# nn.Module的子类神经网络自动跟踪模型对象中定义的所有字段
# 让你可以使用模型的parameters()方法或named_parameters()方法访问所有参数
print(f"Model structure: {model}\n\n")
# 遍历每一个参数并打印其大小以及预览其值
for name, param in model.named_parameters():
    print(f"Layer: {name} | Size: {param.size()} | Values: {param[:2]}\n")
```

### 微分

在训练神经网络时，最常用的算法是反向传播

在该算法中，根据损失函数相对于给定参数的梯度来调整模型参数（模型权重）

为了计算这些梯度，PyTorch有一个内置的微分引擎，名为`torch.autograd`，它支持任何计算图的梯度自动计算

考虑最简单的单层神经网络，输入x，参数w和b，以及一些损失函数。它可以在PyTorch中以以下方式定义：

```py
import torch

# 输入张量
x = torch.ones(5)
# 期望输出，比如说数据的真实标签
y = torch.zeros(3)
# 生成服从标准正态分布（Standard Normal Distribution，也称高斯分布）的随机浮点数
# requires_grad=True，追踪该张量的所有计算历史，以便在后续自动计算梯度
# PyTorch就会在后台偷偷画一张计算图（Computational Graph），记录这个张量参与的所有数学操作
# 也可以在张量创建后通过"张量变量名称.requires_grad_(True) "来设置
w = torch.randn(5, 3, requires_grad=True)
b = torch.randn(3, requires_grad=True)
# 模型的原始输出，通常是nn.Linear直接输出的结果，取值范围是负无穷到正无穷
z = torch.matmul(x, w) + b
# 根据模型的原始输出（z）和期望输出（y）来计算二元交叉熵损失（Binary Cross-Entropy Loss）
# 在普通的二分类任务中，我们需要先对 z 使用 Sigmoid 函数将其压缩到[0, 1] 之间变成概率，然后再计算交叉熵损失
# 这个方法非常智能，它把 Sigmoid 激活和计算交叉熵这两步合二为一了
# 直接把 z 传给它，它会在内部自动完成 Sigmoid 转换，然后计算出预测概率与期望输出 y 之间的差异
loss = torch.nn.functional.binary_cross_entropy_with_logits(z, y)
```

上面的代码定义的计算图如下：

![PyTorchComputationalGraph](image/PyTorchComputationalGraph.png)

在这个神经网络中，w和b是需要我们优化的参数

因此，我们需要能够计算损失函数相对于这些参数的梯度

为此，我们将这些张量的梯度属性设置为`requires_grad=True`

我们应用于张量来构造计算图的方法实际上是Function类的对象

该对象知道如何在正向方向上进行计算，以及如何在反向传播步骤中计算其导数

对反向传播方法的引用存储在张量的`grad_fn`属性中

这个计算图叫：directed acyclic graph (DAG，有向无环图)

在DAG中，输入张量是叶子张量，输出张量是根张量

通过从根到叶的跟踪图，您可以使用链式法则自动计算梯度

- 在向前传递中，autograd同时做两件事
    - 运行请求的操作来计算结果张量
    - 在DAG中维持操作的梯度函数

- 向后传递开始于DAG的根节点调用`.backward()`,然后autograd做
    - 从每个`.grad_fn`计算梯度
    - 在各自张量中的`.grad`属性中进行累加
    - 用链式法则，一直推广到叶子张量

PyTorch中的DAG是动态的，需要注意的一点是图形是从头开始重新创建的

每个`.backward()`调用之后， autograd开始填充一个新图

这正是允许您在模型中使用控制流语句的原因

如果需要，您可以在每次迭代中更改形状、大小和操作

#### 计算梯度

简单描述一下导数：假设`y=wx+b`，那么导数`y'=w`，即斜率w就是导数

为了优化神经网络中参数的权重，我们需要计算损失函数对参数的导数

即x和y为某些固定值时，`∂loss/∂w` 和`∂loss/∂b`的值，到这里笔者已经懵了

为了计算这两个导数，调用`loss.backward()`，然后获取其值用`w.grad`和`b.grad`

```py
loss.backward()
print(w.grad)
print(b.grad)
```

我们只能获取计算图“叶子节点”（Leaf Nodes）的梯度（grad），并且这些节点必须开启了梯度追踪（requires_grad=True）

对于计算图中的其他中间节点，PyTorch 默认不会保存它们的梯度

出于性能原因，我们只能在给定的图上使用一次backward

如果我们需要对同一个图进行几次backward调用，我们需要将`retain_graph=True`传递给backward方法

#### 禁用梯度追踪

默认情况下，设置了`requires_grad=True`的所有张量都会追踪他们的计算历史并支持梯度计算

- 然而，也有一些场景我们不需要这样做，例如
    - 模型已经训练好，我们只是想将输入应用到模型，也就是说我们只想通过神经网络只进行向前计算
    - 将神经网络中的一些参数标记为冻结参数
    - 只进行向前计算时，在不跟踪梯度的张量上的计算会更有效率

我们可以通过在计算代码周围加上`torch.no_grad()`块来禁用追踪计算

也可以调用张量的`detach()`方法来实现

```py
z = torch.matmul(x, w)+b
print(z.requires_grad)

with torch.no_grad():
    z = torch.matmul(x, w)+b
print(z.requires_grad)

z = torch.matmul(x, w)+b
z_det = z.detach()
print(z_det.requires_grad)
```

### 优化模型参数

#### 超参数

超参数是可调节的参数，可以控制模型优化过程

不同的超参数值会影响模型的训练和收敛速度

- 我们为训练模型定义如下超参数
    - Number of Epochs：训练的轮次（epoch），每一轮都训练完整个数据集
    - Batch Size：批次，在参数更新之前，通过神经网络传播的数据样本数量
    - Learning Rate：在每个批次/轮次中需要更新多少模型参数
        - 较小的值产生较慢的学习速度，而较大的值可能导致训练过程中不可预测的行为

```py
# e表示10的幂，1e-3就是1乘以10的-3次方，即0.001
learning_rate = 1e-3
batch_size = 64
epochs = 5
```

#### 优化循环

一旦设置超参数，就可以通过优化循环来训练和优化模型了

优化循环的每一次迭代称为一个epoch

- 每个epoch有两个主要部分组成
    - The Train Loop：训练循环，迭代整个训练数据集并尝试收敛到最优参数
    - The Validation/Test Loop：验证/测试循环，迭代整个测试数据集以检查模型性能是否有所改善

#### 损失函数

当提供一些训练数据时，我们的未经训练的神经网络很可能不会给出正确的答案

损失函数衡量的是得到的结果与目标值的不相似程度，这是我们在训练中要最小化的损失函数

为了计算损失，我们使用给定数据样本的输入进行预测，并将其与真实的数据标签值进行比较

- 常见的损失函数包括
    - `nn.MSELoss`：Mean Square Error，均方误差，用于回归任务
    - `nn.NLLLoss`：Negative Log Likelihood，负对数似然，用于分类任务
    - `nn.CrossEntropyLoss`：Cross Entropy，交叉熵， 整合了nn.LogSoftmax和nn.NLLLoss，

```py
# 初始化损失函数，使logits归一化并计算预测误差
loss_fn = nn.CrossEntropyLoss()
```

#### 优化器

优化是在每个训练步骤中调整模型参数以减少模型误差的过程

优化算法定义如何执行此过程，所有优化逻辑都封装在optimizer对象中

```py
# Stochastic Gradient Descent，随机梯度下降算法
# 此外还有ADAM、RMSProp，应该根据模型和数据分情况使用
# model.parameters()：需要训练的模型参数
# lr：超参数Learning Rate
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)
```

- 在训练循环中，优化分三步进行
    - 调用`optimizer.zero_grad()`来重置模型参数的梯度，梯度默认是加起来的；为了防止重复计算，我们在每次迭代时显式地将它们归零
    - 调用`loss.backward()`反向传播预测损失，PyTorch 会计算并存储损失函数相对于每个参数的梯度
    - 一旦我们得到了梯度，就可以调用 optimizer.step() 方法，利用反向传播过程中收集到的梯度来调整参数

我们定义了 train_loop 来循环执行优化代码，并定义了 test_loop 来评估模型在测试集上的表现

```py
def train_loop(dataloader, model, loss_fn, optimizer):
    size = len(dataloader.dataset)
    # Set the model to training mode - important for batch normalization and dropout layers
    # Unnecessary in this situation but added for best practices
    model.train()
    # batch：当前批次的索引
    # X：特征
    # y：标签
    for batch, (X, y) in enumerate(dataloader):
        # Compute prediction and loss
        pred = model(X)
        loss = loss_fn(pred, y)

        # Backpropagation
        loss.backward()
        # 执行模型参数的更新
        optimizer.step()
        # 清空梯度
        optimizer.zero_grad()

        if batch % 100 == 0:
            loss, current = loss.item(), batch * batch_size + len(X)
            print(f"loss: {loss:>7f}  [{current:>5d}/{size:>5d}]")


def test_loop(dataloader, model, loss_fn):
    # Set the model to evaluation mode - important for batch normalization and dropout layers
    # Unnecessary in this situation but added for best practices
    model.eval()
    size = len(dataloader.dataset)
    num_batches = len(dataloader)
    test_loss, correct = 0, 0

    # Evaluating the model with torch.no_grad() ensures that no gradients are computed during test mode
    # also serves to reduce unnecessary gradient computations and memory usage for tensors with requires_grad=True
    with torch.no_grad():
        for X, y in dataloader:
            pred = model(X)
            test_loss += loss_fn(pred, y).item()
            # argmax， 1表示维度，从模型的预测结果中，找出概率最高的那个类别的索引
            correct += (pred.argmax(1) == y).type(torch.float).sum().item()

    test_loss /= num_batches
    correct /= size
    print(f"Test Error: \n Accuracy: {(100*correct):>0.1f}%, Avg loss: {test_loss:>8f} \n")
```

### 保存和加载模型

PyTorch 模型会将学习到的参数（即权重和偏置）保存在一个内部的状态字典（state_dict）中

这些（参数）可以通过 `torch.save` 方法进行持久化保存

```py
import torch
import torchvision.models as models

# 从 PyTorch 的模型库中调用 VGG16 神经网络
# 并自动下载、加载官方在 ImageNet 数据集上预先训练好的权重（V1版本）
model = models.vgg16(weights='IMAGENET1K_V1')
# 保存模型
# model_weights.pth：文件名，也可以是.pt后缀，这样一看就知道是pytorch的模型权重文件
torch.save(model.state_dict(), 'model_weights.pth')


# 先创建一个相同模型的实例来加载模型权重，然后用load_state_dict()加载模型参数
# weights_only=True，目的是在反序列化（unpickling）过程中，限制只执行加载权重所必需的函数
# 在 Python 中，pickle 是一种用来保存和读取数据的机制
# 但它有一个安全隐患：在读取文件时，它可能会执行文件中潜藏的恶意代码
# 加上 weights_only=True 这个参数，就相当于给 PyTorch 加了一道“安全锁”
# 告诉它：“我只需要提取模型参数，千万别执行其他任何多余的代码”，从而有效防止恶意攻击
# 在加载模型权重时，使用 weights_only=True 被认为是一种最佳实践

# 不用指定权重参数，这里只需创建一个未经训练的模型
model = models.vgg16()
# 加载模型参数
model.load_state_dict(torch.load('model_weights.pth', weights_only=True))

# 设置模型的模式为评估模式
model.eval()
```

#### 保存和加载包含Shapes的模型

这是torch.save遗留用法，了解即可

```py
# 当加载模型权重时，我们需要首先实例化模型类，因为模型类定义了神经网络的结构
# zhe s望将该类的结构与模型一起保存，在这种情况下，可以传model参数，而不是model.state_dict()
torch.save(model, 'model.pth')

# 加载的时候，设置weights_only=False，因为涉及到加载模型
# 这种方法在序列化模型时使用了 Python 的 pickle 模块
# 因此在加载模型时，必须确保原始的类定义仍然可用
model = torch.load('model.pth', weights_only=False)
```

### 完整的训练和预测代码

```py
import torch
from torch import nn
from torch.utils.data import DataLoader
# PyTorch提供了各种领域的库，如TorchText, TorchVision, TorchAudio, 都包含了数据集，这里使用TorchVision数据集
from torchvision import datasets
from torchvision.transforms import v2
import random

# 加载训练数据集
tranning_data = datasets.FashionMNIST(
    root = "data",
    train = True,
    download= True,
    # Compose 是一个“组合器”，它的作用是将列表中的多个变换（transforms）打包在一起，并按照列表中的顺序依次执行
    # 第一步，v2.ToImage()，将输入的图像统一转换为 PyTorch 的 tv_tensors.Image 类型
    # 第二步，v2.ToDtype(torch.float32, scale=True)，转换图像张量的数据类型
    # torch.float32，将像素值的数据类型转换为 32位浮点数
    # scale=True，这是关键参数。当设置为 True 时，它会自动将像素值从整数范围 [0, 255] 线性缩放（归一化）到浮点数范围 [0.0, 1.0]
    # 也就是说将原始的整数像素值除以255.0
    transform= v2.Compose([v2.ToImage(), v2.ToDtype(torch.float32, scale=True)])
)

# 加载测试数据集
test_data = datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=v2.Compose([v2.ToImage(), v2.ToDtype(torch.float32, scale=True)])
)

# e表示10的幂，1e-3就是1乘以10的-3次方，即0.001
learning_rate = 1e-3
batch_size = 64
epochs = 5

# Dataset每次检索数据集中一个样本的特征和标签
# 训练一个模型的时候，我们通常希望以小批量的方式来传递样本
# 在每次遍历完整数据集（一个epoch）时，重新排序（reshuffle）数据以减少模型过拟合
# 并使用Python的多处理来加速数据检索
train_dataloader = DataLoader(tranning_data, batch_size=batch_size, shuffle=True)
test_dataloader = DataLoader(test_data, batch_size=batch_size, shuffle=True)

# 如果当前有加速器可用，我们希望用加速器训练我们的模型，否则还是用CPU
device = torch.accelerator.current_accelerator().type if torch.accelerator.is_available() else "cpu"

print(f"Using {device} device")

# X是图像张量
# y是图像对应的label
for X, y in test_dataloader:
    X, y = X.to(device), y.to(device)
    print(f"Shape of X [Number/Batch Size, Channels, Height, Width]: {X.shape}")
    print(f"Shape of y: {y.shape} {y.dtype}")
    break

# 通过派生nn.Module的子类来定义我们自己的神经网络
class NeuralNetwork(nn.Module):
    # 在构造方法中初始化神经网络层
    def __init__(self):
        super().__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            # 最后一层的输出大小设置为分类的大小，这里是10
            nn.Linear(512, 10)
        )

    # 实现forward方法来操作输入数据
    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits


# 创建神经网络实例，并将移动到指定设备
model = NeuralNetwork().to(device)

# 初始化损失函数，使logits归一化并计算预测误差
loss_fn = nn.CrossEntropyLoss()

# Stochastic Gradient Descent，随机梯度下降算法
# 此外还有ADAM、RMSProp，应该根据模型和数据分情况使用
# model.parameters()：需要训练的模型参数
# lr：超参数Learning Rate
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)

# 在单个训练循环中，模型对训练数据集进行预测（批量提供给它）
# 并反向传播预测误差以调整模型的参数
def train_loop(dataloader, model, loss_fn, optimizer):
    size = len(dataloader.dataset)
    # 设置模型的模式为训练模式，这对批归一化（Batch Normalization）和 Dropout 层非常重要
    # 在当前情况下其实并非必需，但加上这是为了遵循最佳实践
    model.train()
    # batch：当前批次的索引
    # X：特征
    # y：标签
    for batch, (X, y) in enumerate(dataloader):
        X, y = X.to(device), y.to(device)
        # 计算预测值和损失
        pred = model(X)
        loss = loss_fn(pred, y)

        # 反向传播
        loss.backward()
        # 执行模型参数的更新
        optimizer.step()
        # 清空梯度
        optimizer.zero_grad()

        if batch % 100 == 0:
            loss, current = loss.item(), batch * batch_size + len(X)
            print(f"loss: {loss:>7f}  [{current:>5d}/{size:>5d}]")


# 根据测试数据集检查模型的性能，以确保它正在学习
def test_loop(dataloader, model, loss_fn):
    # 设置模型的模式为评估模式，这对批归一化（Batch Normalization）和 Dropout 层非常重要
    # 在当前情况下其实并非必需，但加上这是为了遵循最佳实践
    model.eval()
    size = len(dataloader.dataset)
    num_batches = len(dataloader)
    test_loss, correct = 0, 0

    # 使用 torch.no_grad() 来评估模型，可以确保在测试模式下不计算梯度
    # 还能有效减少不必要的梯度计算，并降低 requires_grad=True 的张量所带来的内存占用
    with torch.no_grad():
        # X：特征
        # y：标签        
        for X, y in dataloader:
            X, y = X.to(device), y.to(device)
            pred = model(X)
            test_loss += loss_fn(pred, y).item()
            # argmax， 1表示维度，从模型的预测结果中，找出概率最高的那个类别的索引
            correct += (pred.argmax(1) == y).type(torch.float).sum().item()

    test_loss /= num_batches
    correct /= size
    print(f"Test Error: \n Accuracy: {(100*correct):>0.1f}%, Avg loss: {test_loss:>8f} \n")

# 训练过程在几个迭代（epoch）中进行，这里设置为5轮
# 在每一轮中，模型学习参数以做出更好的预测
# 我们打印出模型在每轮的精度和损失
# 我们希望看到精度随着时间的推移而提高，损失随着时间的推移而减少
for t in range(epochs):
    print(f"Epoch {t+1}\n-------------------------------")
    train_loop(train_dataloader, model, loss_fn, optimizer)
    test_loop(test_dataloader, model, loss_fn)
print("Done!")

# 保存模型
# model_weights.pth：文件名，也可以是.pt后缀，这样一看就知道是pytorch的模型权重文件
torch.save(model.state_dict(), 'model_weights.pth')
print("Saved PyTorch Model State to model_weights.pth")

# 加载模型
model = NeuralNetwork().to(device)
model.load_state_dict(torch.load("model_weights.pth", weights_only=True))

# 预测
classes = [
    # T恤
    "T-Shirt/top",
    # 裤子
    "Trouser",
    # 套衫
    "Pullover",
    # 裙子
    "Dress",
    # 外套
    "Coat",
    # 凉鞋
    "Sandal",
    # 衬衫
    "Shirt",
    # 运动鞋
    "Sneaker",
    # 包
    "Bag",
    # 踝靴：一种只到踝部的靴子，通常用于保护踝关节
    "Ankle Boot",
]

# 设置模型的模式为评估模式
model.eval()

# 随机取出测试数据集中的一个特征和标签
datasetIndex = random.randint(0, 9999)
x, y = test_data[datasetIndex][0], test_data[datasetIndex][1]

# 禁用梯度追踪
with torch.no_grad():
    x = x.to(device)
    pred = model(x)
    predicted, actual = classes[pred[0].argmax(0)], classes[y]
    result = predicted==actual
    print(f'datasetIndex: {datasetIndex}, Predicted: "{predicted}", Actual: "{actual}", Predicted Result: {result}')
```
