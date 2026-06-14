# AI Note

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
