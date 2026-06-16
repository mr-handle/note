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

## Ollama

Ollama是一个类似docker的用于本地快速部署模型进行推理的工具，很多命令都跟docker相似

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
# 如果是and显卡，除了rocm-hip-sdk，还需要安装ollama-rocm才能使用GPU加速
sudo pacman -S ollama ollama-rocm

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
ollama run  模型名称:标签

# 停止运行的模型
# 即使在终端停止了模型，只要用桌面客户端应用连接了ollama，发送消息就会唤醒或启动模型
ollama stop 模型名称:标签
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
