# 一、深度学习的核心：PyTorch 框架

## 1. 什么是深度学习框架？

如果你要把一堆散乱的砖头建成大厦，你不需要从烧砖、造水泥开始，而是使用现成的起重机和脚手架。**深度学习框架**就是 AI 领域的“起重机”。

在没有框架的年代，开发者需要手动编写复杂的数学公式来计算神经元的更新。而有了 PyTorch 这样的框架：

- **自动化**：它帮你处理了最头疼的“反向传播”（自动计算梯度）。
- **硬件加速**：它能自动调用 NVIDIA 显卡的 **CUDA** 核心，让计算速度比 CPU 快上百倍。
- **生态丰富**：目前 80% 以上的 AI 论文和像 Llama、Qwen 这样的开源大模型，都是基于 PyTorch 开发的。
## 2. PyTorch 的核心：张量 (Tensor) 与计算图

要理解 PyTorch，你只需要记住一个核心概念：**张量 (Tensor)**。

#### 什么是张量？

在 Python 基础中，你一定学过“列表 (List)”或“数组 (Array)”。张量可以简单理解为**“运行在 GPU 上的多维数组”**。

- **0维张量**：一个数字（标量）。    
- **1维张量**：一行数字（向量）。
- **2维张量**：一个表格（矩阵）。
- **多维张量**：三维及以上的数组（例如一张彩色图片是由长、宽、颜色通道组成的三维张量）。

$$\text{Scalar} \rightarrow \text{Vector} \rightarrow \text{Matrix} \rightarrow \text{Tensor}$$

#### 为什么张量如此重要？

大模型的本质就是**极大规模的数学运算**。

当你对模型说一句话时，这句话会被转化成一串数字（张量），然后与模型内部数以亿计的权重（也是张量）进行相乘和相加。PyTorch 的核心任务就是高效地完成这些**张量积（Tensor Multiplication）**运算。

---

## 3. GPU 与 CUDA：加速的秘诀

为什么微调模型一定要用显卡（GPU）？

- **CPU**：像一个全能的数学教授，虽然聪明，但一次只能算几道复杂的题。
- **GPU**：像上千名小学生，虽然只会简单的加减乘除，但他们可以**同时**计算几千道简单的题。

由于深度学习本质上是海量的简单加减乘除（张量运算），GPU 的这种“人多力量大”的并行计算特性，使其成为了不二之选。而 **CUDA**，就是显卡厂商 NVIDIA 推出的、让 PyTorch 能够指挥显卡进行数学运算的“指挥棒”。

不过，现在安装Pytorch并不单独安装CUDA，相关需要的内容已经集成在Pytorch的库里了。


# 二、Pytorch的安装

在环境搭建的最后一步，我们要安装整个微调流程的“心脏”——**PyTorch**。这一步是初学者最容易出错的地方，因为 PyTorch 的版本必须与你的显卡驱动精准匹配。
## 1. PyTorch 是什么？

从开发者的角度看，PyTorch 就是一个 **Python 库**。 就像你使用 `import pandas` 来处理数据一样，我们通过 `import torch` 来调用深度学习的各项功能。它把复杂的数学运算（矩阵乘法、梯度计算）封装成了简单易用的 Python 函数。

## 2. CPU 版本 vs GPU 版本

这是初学者最常踩的第一个坑：

- **CPU 版本**：如果你直接在终端输入 `pip install torch`，默认下载的往往是 CPU 版本。它只能利用电脑的处理器进行计算，速度极慢，**无法用于大模型微调**。
- **GPU 版本 (CUDA)**：专门为 NVIDIA 显卡优化。它包含了一个名为 **CUDA** 的工具包，能驱动显卡成千上万个核心同步计算。
- **如何区分**：在 PyTorch 的版本号中，带有 `+cu118` 或 `+cu121` 后缀的才是支持显卡加速的 GPU 版本。



# 三、在线安装

安装 PyTorch **绝对不能**盲目输入命令，必须去官网获取属于你电脑的“专属指令”。
#### 第一步：检查你的 CUDA 版本
![](tutorials/1_环境配置/img/nvidia-smi.png)
在安装之前，你需要知道你的显卡驱动支持到哪个版本的 CUDA。
1. 打开终端（Anaconda Prompt）。
2. 输入命令：`nvidia-smi`。
3. 在右上角找到 `CUDA Version: XX.X`。记下这个数字（例如 12.1）。

#### 第二步：访问 PyTorch 官网

前往 [PyTorch.org](https://pytorch.org/)首页往下划动，你会看到一个配置矩阵：

![](tutorials/1_环境配置/img/Pytorch_run_command.png)

| **配置项**              | **选择建议**                                                |
| -------------------- | ------------------------------------------------------- |
| **PyTorch Build**    | 选择 **Stable (稳定版)**                                     |
| **Your OS**          | 根据你的系统选择 Windows 或 Linux                                |
| **Package**          | 选择 **Pip** (最简单直接)                                      |
| **Language**         | 选择 **Python**                                           |
| **Compute Platform** | **关键！** 选择和你 `nvidia-smi` 对应或略低于它的 CUDA 版本（如 CUDA 12.1） |

这里只显示最近的几个CUDA版本，如果你的CUDA版本低于这些，可以点击左下角的`Previous versions of Pytorch`下载以前的版本。[Previous PyTorch Versions](https://pytorch.org/get-started/previous-versions/)

![](tutorials/1_环境配置/img/previous_cuda_command.png)
搜索一下，找到自己对应的CUDA 版本。复制下面的命令。
现在我们得到了正确的下载指令，如
```bash
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124
```

#### 第三步：安装

首先打开 Conda终端，
- 先激活我们之前创建的环境
- 然后粘贴上面的下载指令
```shell
conda activate llm-finetune
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124
```

![](tutorials/1_环境配置/img/pytorch_install.png)
#### 验证安装成功

```bash
python 
import torch
print(torch.cuda.is_availabel())
exit()
```


- python: 在终端运行python代码
- import torch: 导入torch库
- print(torch.cuda.is_availabel())：验证pytorch的CUDA是否可用
- exit()：退出终端python代码运行
# 四、离线安装

当 `pip install` 在线下载速度极慢或频繁断开时，我们可以采取“**先下载离线包，再本地安装**”的策略。

## 1. 核心安装包简介

PyTorch 家族主要由三个库组成。对于**大语言模型 (LLM)** 的微调，我们通常只需要安装 `torch`。

- **`torch`**：核心库（必装）。包含张量计算、自动求导和神经网络组件。
- **`torchvision`**：视觉扩展库。处理图像、视频数据时需要。
- **`torchaudio`**：音频扩展库。处理语音识别、音频转换时需要。

## 2. 下载地址与版本选择

你可以前往 PyTorch 官方的离线仓库（whl 托管站）寻找对应的文件：
- **稳定版汇总 (推荐)**：[torch_stable_whl](https://www.google.com/search?q=https://download.pytorch.org/whl/torch_stable.html)
- **全部版本索引**：[Index of /whl/](https://download.pytorch.org/whl/)

## 3. 安装包命名规则解析

下载前，必须看懂文件名，否则安装时会提示 `is not a supported wheel on this platform`。

**示例文件名：**

`torch-2.4.0+cu124-cp310-cp310-win_amd64.whl`

| **组成部分**           | **含义**                        | **如何确认**                   |
| ------------------ | ----------------------------- | -------------------------- |
| **torch-2.4.0**    | PyTorch 的版本号                  | 建议选最新的稳定版                  |
| **+cu124**         | **CUDA 版本**：这里指 CUDA 12.4     | 终端输入 `nvidia-smi` 查看       |
| **cp310**          | **Python 版本**：这里指 Python 3.10 | 终端输入 `python --version` 查看 |
| **win / linux**    | **操作系统**：Windows 或 Linux      | 根据你的系统选择                   |
| **amd64 / x86_64** | **架构**：64 位系统                 | 现代电脑几乎都是 64 位              |

## 4. 安装方法

1. **下载**：将对应的 `.whl` 文件下载到电脑。
2. **定位**：在终端（Anaconda Prompt）中进入该文件所在的文件夹。
    - 例如：`cd Downloads`
3. **执行安装**：使用 `pip install` 后面跟上完整的**文件名**。

```bash
# 记得先激活环境
conda activate llm-finetune 
# 示例：安装当前目录下下载好的 torch 包
pip install torch-2.4.0+cu124-cp310-cp310-win_amd64.whl
```

## 💡 常见问题排查

- **依赖缺失**：PyTorch 运行还需要一些基础包（如 `fsspec`, `jinja2`, `networkx`）。如果离线安装报错缺库，建议在有网的环境下先执行 `pip download <包名>` 把依赖也下好。    
- **文件名错误**：不要去重命名下载下来的 `.whl` 文件，否则 `pip` 可能会无法识别其内部的校验信息。