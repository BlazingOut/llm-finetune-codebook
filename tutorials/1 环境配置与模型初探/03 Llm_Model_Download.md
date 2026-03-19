
Hugging Face作为当前最流行的AI模型社区，汇集了数十万个预训练模型。但对于国内开发者来说，模型下载常常面临网络不稳定、速度慢等问题。本文将详细介绍三种主流下载方法，帮助你高效获取所需模型。

# 一、 Hugging Face 基础使用

## 1. 访问 Hugging Face

**官网地址：** [huggingface.co](https://huggingface.co/)
由于网络环境限制，直接访问官网可能会遇到连接不稳定的情况。针对国内开发者，推荐以下两种解决方案：
#### 方案 A：使用国内镜像站（推荐）
- **网址：** [HF-Mirror (hf-mirror.com)](https://hf-mirror.com/)    
- **特点：** 内容与官网实时同步，下载速度快，无需特殊网络环境。

#### 方案 B：使用网络优化工具 (Steamcommunity 302)
这是一款由开发者 `Dogfight360` 维护的免费工具，最新版本已支持 Hugging Face 的访问加速。
**使用步骤：**
1. **下载：** 访问 [Dogfight360 官网](https://www.dogfight360.com/blog/475/) 下载最新版本。
2. **配置：** 解压并运行主程序，点击 **“设置”**。
3. **勾选：** 在功能列表中找到 **“HuggingFace”** 并勾选。
4. **生效：** 点击“保存设置”，回到主界面点击 **“启动服务”**。

![[steamcommunity302_start.png]]

![[steamcommunity302_settings.png]]

---

## 2. 认识平台核心功能

在 Hugging Face 顶部导航栏，你会看到三个核心板块：

| **仓库类型**     | **用途**   | **核心价值**                         |
| ------------ | -------- | -------------------------------- |
| **Models**   | **模型库**  | 下载预训练权重（如 Llama, Qwen），进行微调或推理。  |
| **Datasets** | **数据集**  | 获取微调所需的训练数据（如 Alpaca, WikiText）。 |
| **Spaces**   | **应用展示** | 在线试用别人部署好的 AI 应用（类似 Demo 展示）。    |

> **微调指南：** 在本项目中，我们主要打交道的是 **Models**（获取底座）和 **Datasets**（准备材料）。

![[huggingface_first_page.png]]

---

## 3. 定位与搜索目标模型

在搜索框中输入我们要使用的模型 ID。例如，本次教程推荐的入门级模型：`Qwen/Qwen2.5-1.5B-Instruct`。

![[qwen2.5_model_page.png]]

#### 🔍 模型命名深度解析

理解模型的命名规则，能帮你快速判断该模型是否适合你的显卡：

- **Qwen**：模型作者/厂商（阿里云通义千问团队）。
- **2.5**：系列版本号（版本越高，通常架构越优化）。
- **1.5B**：参数量大小。`B` 代表 Billion（十亿）。**1.5B 即 15 亿参数**，这对消费级显卡（如 RTX 3060/4060）非常友好。
- **Instruct**：模型类型。表示该模型经过**指令微调**，可以直接进行对话；若没有该后缀（如 Base 版），则主要用于续写文本。


这一部分涉及到具体的工程实践，排版的重点在于**区分操作系统**、**突出核心命令**以及**解释关键参数**。我为你整理了一个更加直观的版本。

---

# 二、 模型下载实战

下载模型通常有“手动”和“自动”两种逻辑。对于开发者，**推荐优先使用命令行工具**，因为它支持断点续传且更易于管理。

## 1. 方式一：网页手动下载 (适合单文件或小模型)

你可以直接在 Hugging Face 官网或镜像站的 **Files and versions** 选项卡中看到模型的所有文件。

#### 📌 主流源对比

|**来源**|**网址**|**特点**|**适用场景**|
|---|---|---|---|
|**Hugging Face**|[huggingface.co](https://huggingface.co/)|官方源，最全最新|海外服务器/稳定网络|
|**HF-Mirror**|[hf-mirror.com](https://hf-mirror.com/)|社区维护，实时同步|**国内环境首选（推荐）**|
|**ModelScope**|[modelscope.cn](https://modelscope.cn/)|阿里维护，CDN加速|国内生产环境、国产模型|

#### 📁 核心文件识别

下载模型时，请务必确保包含以下核心组件（缺一不可）：

- **`config.json`**：模型的结构参数（如层数、隐藏层维度）。
- **`*.safetensors` 或 `pytorch_model.bin`**：模型的核心权重文件。
- **`tokenizer.json` / `vocab.json`**：分词器配置与词表，负责将文字转为数字。

![[qwen2.5_file_download_page.png]]

---

## 2. 方式二：命令行工具下载 (专业推荐)

#### 方法 A：使用 `huggingface-cli` (官方推荐)

这是最通用的工具，支持断点续传和多种配置。

**1. 安装工具**

在终端（Terminal/PowerShell）执行：
```bash
pip install -U huggingface_hub
```

**2. 配置国内镜像环境变量**

为了加速下载，我们需要将 API 地址指向国内镜像站。

- **Linux / Mac：**
    ```bash
    # 临时生效
    export HF_ENDPOINT=https://hf-mirror.com
    # 永久生效：建议添加到 ~/.bashrc 或 ~/.zshrc
    echo 'export HF_ENDPOINT=https://hf-mirror.com' >> ~/.bashrc
    source ~/.bashrc
    ```
- **Windows：**
    - **CMD:** `set HF_ENDPOINT=https://hf-mirror.com`
    - **PowerShell:** `$env:HF_ENDPOINT="https://hf-mirror.com"`

**3. 执行下载**

使用以下推荐命令，可以确保下载到指定文件夹并支持断点续传

```bash
# 格式：huggingface-cli download --resume-download <模型ID> --local-dir <本地路径>
huggingface-cli download --resume-download Qwen/Qwen2.5-1.5B --local-dir ./qwen2_model --token hf_***
```

> **参数详解：**
-  `--resume-download`：**必选**。网络中断后重新执行，会从上次进度继续，不会重头开始。
-  `--local-dir`：指定保存路径，方便后续代码读取。
-  `--token`：用于下载访问受限模型。见[[#三、访问受限模型]]

---

#### 方法 B：使用 `hfd` 脚本 (适合 Linux/极速需求)

`hfd` 是基于 `aria2` 开发的轻量级脚本，多线程下载速度通常更快。

**1. 环境准备 (仅限 Linux)**
```bash
# 下载脚本并赋予执行权限
wget https://hf-mirror.com/hfd/hfd.sh
chmod a+x hfd.sh

# 同样需要设置镜像环境变量
export HF_ENDPOINT=https://hf-mirror.com
```

**2. 下载模型/数据集**
```bash
# 下载模型
./hfd.sh Qwen/Qwen2.5-1.5B --tool aria2

# 下载数据集
./hfd.sh <数据集名称> --dataset
```

---

## ⚠️ 常见问题排查 (Troubleshooting)

1. **Permission Denied (权限拒绝)：** 如果下载受限模型（如 Llama 3.1），请确保你已执行过 `huggingface-cli login` 并输入了前文提到的 **Access Token**。
    
2. **下载极慢或者报错Repo之类的错误：** 检查 `HF_ENDPOINT` 环境变量是否设置成功。可以在终端输入 `echo $HF_ENDPOINT` (Linux) 或 `$env:HF_ENDPOINT` (PS) 确认。

---

## 3. 方式三：使用 Python 代码下载模型

Python 代码下载是最灵活的方式，适合集成在训练脚本或 Jupyter Notebook 中。这种方式能让你在代码运行之初就确保模型环境就绪。

### 核心库准备

在开始之前，请确保安装了 Transformers 核心库：
```bash
pip install -U transformers huggingface_hub
```
---
### 方法A：通过 Transformers 库自动触发

这是最简单的方法。当你调用 `from_pretrained` 时，系统会检查本地是否有缓存，如果没有，则自动启动下载。

#### 基础加载与下载

```python
import os
from transformers import AutoModel, AutoTokenizer

# 💡 建议在导入 transformers 前设置镜像源，确保下载顺畅
os.environ["HF_ENDPOINT"] = "https://hf-mirror.com"

model_name = "Qwen/Qwen2.5-1.5B-Instruct"

# 第一次运行此代码时，程序会自动从镜像源下载模型到默认缓存目录
# 默认路径通常在：~/.cache/huggingface/hub (Linux) 或 C:\Users\用户名\.cache\huggingface\hub (Win)
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)

print("模型与分词器已成功加载/下载")
```

#### 自定义缓存路径

如果你担心 C 盘空间不足，可以通过 `cache_dir` 参数指定存放位置：

```python
model = AutoModel.from_pretrained(
    "Qwen/Qwen2.5-1.5B-Instruct",
    cache_dir="./my_model_weights"  # 将模型缓存到当前目录下的指定文件夹
)
```

---

### 方法B：使用 `huggingface_hub` (进阶推荐)

如果你只需要**单纯地下载文件**而不立即加载进显存（例如在准备训练环境时），使用 `snapshot_download` 是最专业的选择。

#### 完整下载示例

该方法支持**断点续传**，并且可以清晰地指定存放目录，避免了缓存文件路径混乱的问题。

```python
import os
from huggingface_hub import snapshot_download

# 1. 设置镜像源
os.environ["HF_ENDPOINT"] = "https://hf-mirror.com"

model_id = "Qwen/Qwen2.5-1.5B-Instruct"
local_save_path = "./qwen2.5-model"

# 2. 执行高速下载
snapshot_download(
    repo_id=model_id,
    local_dir=local_save_path,      # 下载到指定目录
    local_dir_use_symlinks=False,   # 设为 False 直接保存原始文件（推荐初学者使用）
    resume_download=True,           # 开启断点续传
    max_workers=8                   # 增加线程数以加快速度
)

print(f"✅ 模型已完整下载至: {local_save_path}")
```

---

#### 处理受限模型 (如 Llama 3.1)

对于需要权限的模型，你必须在代码中提供我们的 **Access Token**。
见后文的[[#三、访问受限模型]]

```python
from huggingface_hub import login, snapshot_download

# 方式 A：通过 login 函数登录（运行一次即可，信息会保存在本地）
login(token="你的_HF_访问令牌")

# 方式 B：在下载函数中直接传入 token
snapshot_download(
    repo_id="meta-llama/Llama-3.1-8B-Instruct",
    local_dir="./llama3_1",
    token="你的_HF_访问令牌" # 或者设为 True（前提是已执行过 login）
)
```

---

## 💡 助手深度解析：`cache` 与 `local_dir` 的区别

这是初学者最容易掉坑的地方：

- **Cache (缓存)**：如果你不指定目录，HF 会把模型存成一堆混乱哈希命名的文件。虽然 `from_pretrained` 能识别，但你自己很难手动找到它们。
    
- **Local Dir (本地目录)**：使用 `snapshot_download` 并指定 `local_dir`，文件会以你在网页上看到的原始结构（如 `config.json`）整齐排列。**在本项目后续的微调流程中，我们建议统一使用 Local Dir 模式。**

在 Hugging Face (HF) 上，并不是所有的模型都可以直接匿名下载。像 Meta 的 **Llama 3.1** 或 Google 的 **Gemma** 等模型，通常属于“受限访问”资源。


# 三、访问受限模型
### 步骤 1：注册与登录

在开始之前，请确保你已经拥有一个 [Hugging Face](https://huggingface.co/join) 账号。

- **登录：** 访问模型主页（例如 [Llama-3.1-8B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct)）。
- **状态确认：** 如果看到需要登录的提示，请先完成登录。

![[limited_access_model.png]]

### 步骤 2：提交模型使用申请 (Gated Models)

部分模型需要开发者手动或系统自动审批你的申请。

1. 在模型主页填写必要的信息（通常包括姓名、邮箱、国家及用途）。
2. 勾选同意相关的开源协议。
3. 点击 **"Submit"** 提交申请。
    

> **注意：** 审批时间从几分钟到几天不等。审批通过后，你会收到一封确认邮件，模型页面也会显示“你已获得访问权限”。

![[limited_access_success_model.png]]

---

### 步骤 3：创建访问令牌 (Access Token)

为了让你的本地开发环境（Python 代码或终端）能够代表你下载受限模型，你需要创建一个身份证明，即 **Access Token**。

1. **进入设置：** 点击网页右上角的个人头像，选择 **Settings**，新的版本**Settings**的下面可以直接找到 **Access Tokens**。
2. **访问令牌选项：** 在左侧边栏找到 **Access Tokens**。

![[access_tokens_page.png]]

3. **新建 Token：** 点击 **Create new token**。
    

![[add_access_token.png]]

#### 配置建议：

- **Token Type (类型)：** 选择 **Read** (只读)。对于初学者来说，下载模型只需要读取权限，这更安全。
    
- **Token Name (名称)：** 输入一个助记名称（例如 `my_first_finetune`），方便日后管理。
    

### ⚠️ 重要安全提醒

- **一次性展示：** 生成 Token 后，请立即点击 **Copy** 并保存到本地的安全位置（如记事本或密码管理器）。
- **不可找回：** 刷新页面后，你将无法再次查看完整的 Token 字符串。如果丢失，只能删除旧的并重新创建。
- **保密性：** **绝对不要**将包含真实 Token 的代码上传到 GitHub 等公开平台！

---

### 步骤 4：在开发环境中使用

当你使用 `huggingface-cli login` 命令或在 Python 代码中使用 `from_pretrained` 方法时，将你刚刚保存的字符串粘贴进去即可完成身份验证。