
**本章任务：**

1. **配置编译器**：让 VS Code “看懂”你的代码。
2. **集成终端**：让 VS Code “运行”你的指令。
3. **远程连接**：让 VS Code “掌控”远程的 GPU。

## 一、VS Code 配置 Conda 编译器（解释器）

安装完虚拟环境后，我们需要告诉 VS Code 使用哪一个 Python 环境来运行你的代码。
同时给VS Code配置编译环境还有一个作用，它可以调用这个环境来智能地检查你的代码是否有错，并给出修改建议。

### 1. 安装 Python 官方扩展

首先，VS Code 需要具备识别 Python 代码的能力。

1. 打开 VS Code，点击左侧工具栏的 **扩展 (Extensions)** 图标。
2. 在搜索框输入 `Python`，选择由 **Microsoft** 发布的插件并点击 **Install**。

![](tutorials/1_环境配置/img/vscode_python_extension.png)

### 2. 选择 Python 解释器
![](tutorials/1_环境配置/img/vscode_environment_setting.png)

安装插件后，你可以通过以下方式为你的项目指定环境：

1. **新建文件**：随便创建一个以 `.py` 为后缀的文件（例如 `test.py`）。
2. **点击右下角**：观察 VS Code 窗口右下角的状态栏，点击显示 Python 版本的地方（例如 `Select Interpreter` 或 `Python 3.x.x`）。

![](tutorials/1_环境配置/img/vscode_add_python_env.png)

3. **弹出列表**：页面上方会弹出环境列表。如果你能看到刚才创建的虚拟环境（如 `llm-finetune`），直接点击选择即可。

### 3. 如果找不到环境？（手动添加路径）

如果列表中没有出现你的环境，说明 VS Code 没能自动扫描到它。我们可以手动定位：

1. 在弹出的列表顶部选择 **"Enter interpreter path..." (输入解释器路径)**。
2. 点击 **"Find..." (查找)**，在弹出的文件浏览器中找到你的 Miniconda 安装目录。
3. **定位逻辑：**
    - 进入 Miniconda 安装目录下的 **`envs`** 文件夹。
    - 找到你创建的环境名称文件夹（例如 `llm-finetune`）。
    - **关键步骤**：点进去找到 **`python.exe`** 文件，点击 **"Select Interpreter"**。

![](tutorials/1_环境配置/img/how_to_find_conda_environment.png)

---

**💡 助手小贴士：如何判断配置成功？**

配置完成后，VS Code 右下角会显示类似 `('llm-finetune': conda)` 的字样。此时，你在代码中写的 `import` 语句如果报错（波浪线），通常是因为你还没在该环境中安装对应的库。

## 二、在 VS Code 中集成 Conda 终端

虽然我们已经配置了 Python 解释器，但在大模型微调中，我们经常需要手动输入带有参数的命令（如指定显卡、调整训练超参数等），因此在 VS Code 内部集成一个 **Conda 专用终端** 会让开发流程更加丝滑。

### 1. 如何打开内置终端？

- **方案一：** 在 VS Code 顶部菜单栏找到 **终端 (Terminal)** -> **新建终端 (New Terminal)**。
- **方案二（快捷键）：** 按下 `Ctrl + Shift + ~`。
![](tutorials/1_环境配置/img/vscode_terminal.png)

---

### 2. 核心配置步骤

1. **进入设置**：按下快捷键 `Ctrl + ,` (逗号)，或点击左下角齿轮图标选择 **Settings**。
2. **搜索配置项**：在搜索框输入 `automation profile`。
3. **编辑 JSON**：找到 `Terminal > Integrated > Profiles: Windows`（Mac/Linux 用户选择对应系统项），点击下方蓝色的 **Edit in settings.json**。

![](tutorials/1_环境配置/img/vscode_terminal_settings.png)

---

### 3. 修改 `settings.json`

在弹出的 JSON 文件中，找到 `terminal.integrated.profiles.windows` 这一栏，并参照以下内容进行添加。

> **⚠️ 重要提示：** 请务必将下方代码中的 `C:\\Users\\YourUsername\\miniconda3` 替换为你电脑上**真实的安装路径**（注意路径中的斜杠是双斜杠 `\\`）。

```json
"terminal.integrated.profiles.windows": {
    // --- 复制下方内容到原有配置中 ---
    "Conda-Prompt": {
        "path": "cmd.exe",
        "args": [
            "/K",
	        "C:\\Users\\YourUsername\\miniconda3\\Scripts\\activate.bat C:\\Users\\YourUsername\\miniconda3"
        ],
        "icon": "terminal-conda"
    }
    // --- 复制结束 ---
},

// 找到这一行，将默认值由 "Command Prompt" 改为 "Conda-Prompt"
"terminal.integrated.defaultProfile.windows": "Conda-Prompt"
```

📌 **参数解析：**
- **`path`**: 指定该终端的基础外壳为 `cmd.exe`。
- **`args`**: 关键启动参数。它告诉终端在启动时自动运行 Conda 的初始化脚本 (`activate.bat`)，确保你一打开终端就能直接使用 `conda` 命令。
- **`defaultProfile`**: 设置为默认项。这样你以后按 `Ctrl + ~` 启动的永远是带 Conda 环境的终端。

---

### 4. 如何验证是否配置成功？

1. **重启终端**：关闭之前的终端窗口，按 `Ctrl + Shift + ~` 新建一个。
2. **查看标识**：观察终端最左侧。如果出现了 **`(base)`** 或者是你当前激活的环境名，说明配置成功！
3. **手动切换**：如果你想临时换回普通 CMD，可以点击终端面板右上角的 **“+”** 号旁边的下拉箭头，手动选择 `Conda-Prompt` 或其他终端。

![](tutorials/1_环境配置/img/vscode_terminal_check.png)

---

**💡 助手小贴士**

如果你在修改 `settings.json` 时发现 VS Code 报错（右侧滚动条变红），通常是因为**少写了逗号**或者括号没对齐。JSON 格式非常严苛，请务必保持层级一致！

## 三、远程开发神器：VS Code + Remote SSH

如果你的 GPU 位于远程服务器（如实验室服务器或 AutoDL 等算力平台），你不需要在黑漆漆的终端里盲打代码。通过 **Remote SSH** 插件，你可以像操作本地文件夹一样编写远程代码。

### 1. 核心概念简述

- **SSH (Secure Shell)**：一种加密的网络协议，用于安全地登录远程计算机。
- **Remote SSH 插件**：VS Code 的官方扩展。它让你在本地写代码，代码实际保存在服务器上，运行也在服务器上。
    
### 2. 安装与配置步骤

#### 第一步：安装插件

在 VS Code 左侧工具栏点击 **扩展 (Extensions)** 图标（四个方块形状），搜索 `Remote - SSH`，点击 **Install** 安装。

![](tutorials/1_环境配置/img/vscode_remote_ssh_extension.png)

安装完成后，左侧状态栏会多出一个**远程资源管理器**图标（看起来像一个小电脑或两个尖括号 `><`）。

#### 第二步：添加远程连接

![](tutorials/1_环境配置/img/vscode_remote_ssh_link.png)

1. 点击左侧的**远程资源管理器**图标。
2. 点击窗口中 **SSH** 栏目旁的 **“+” (Add New)** 按钮。
3. 在页面上方弹出的输入框中，输入服务器的连接指令：
    ```bash
    # 格式：ssh 用户名@服务器IP地址 -p 端口号
    # 例如：
    ssh root@123.45.67.89 -p 22
    ```
    
    _(注：如果你的服务器使用默认 22 端口，可以省略 `-p 22`；算力平台通常会提供完整的登录指令，直接复制即可。)_
    
4. 按下回车，选择第一个默认的配置文件路径（通常是 `C:\Users\用户名\.ssh\config`）。

#### 第三步：连接并登录

1. 在左侧列表中会出现你刚刚添加的服务器。点击右侧的**新窗口连接图标**。
2. VS Code 会开启一个新窗口，上方会询问服务器类型（选择 **Linux**），然后要求输入 **密码**。
3. 输入密码并回车。当左下角显示 `SSH: [服务器名]` 时，代表连接成功！

---

### 3. 常见问题：正在下载 VS Code Server (卡死/超时)

这是国内初学者最常遇到的“下马威”。

- **原因**：当你连接时，本地 VS Code 会检测远程服务器是否安装了对应版本的 `vscode-server`。如果没有，它会尝试从 GitHub/微软官网下载。由于网络限制，这个几百 MB 的包经常下载失败。
#### 解决方案：

- **方案 A（最简单）**：保持本地电脑的 VPN 开启（开启全局模式），通常等待 2-5 分钟即可自动完成安装。
    
- **方案 B（手动安装）**：如果服务器完全无法访问外网，需要手动下载与你本地 VS Code 版本匹配的 `vscode-server` 压缩包，通过 FTP 软件上传到服务器的 `~/.vscode-server/bin/[Commit ID]/` 目录下。
    
    > **参考教程**：[VScode ssh连接远程服务器 远程无法下载server导致连接失败 - 知乎](https://zhuanlan.zhihu.com/p/1997983644325811822)
    

---

### 4. 进阶建议：配置 SSH Key 免密登录

如果你不想每次打开 VS Code 或切换文件夹都要输一遍密码，建议在本地生成 SSH 公钥并上传到服务器。

1. **本地生成**：在本地终端输入 `ssh-keygen -t rsa`。
2. **上传公钥**：将 `id_rsa.pub` 的内容复制到服务器的 `~/.ssh/authorized_keys` 文件中。
    _这样，下次连接时 VS Code 就会自动完成身份验证，体验极其丝滑。_


## 四、Jupyter notebook


首先介绍一下Jupyter notebook的特点，以及它对于教学的重要性。
然后进入配置阶段

### 1. 如何配置Jupyter notebook 的环境

#### 第一步：在base环境中安装jupyter notebook插件
在base环境安装jupyter notebook就可以了，并且只需要安装这一次

```
#(base)
pip install jupyter notebook
```
其他的环境只需要进行下面的环境配置操作
#### 第二步：激活虚拟环境
```bash
conda create -n my_env python=3.10
conda activate my_env
```

#### 第三步：安装ipykernel
在虚拟环境中安装`ipykernel`包，这是连接Jupyter的关键组件：
```bash
conda install ipykernel -y
```

#### 第四步：将环境注册到Jupyter内核
将当前虚拟环境添加到Jupyter的内核列表，并设置显示名称（如`My_Project`）：
```bash
python -m ipykernel install --user --name=my_env --display-name="My_Project"
```
参数说明：
- `--name`：内核标识（需与环境名一致）。
- `--display-name`：Jupyter界面中显示的名称，可自定义
#### 验证配置

启动Jupyter Notebook：
```bash
# 在任意的环境中都可以，输入
jupyter notebook
```

在Notebook界面点击右上角`Kernel → Change Kernel`，选择刚注册的`My_Project`即可使用该环境

### 2. 在vscode中使用jupyter notebook

- 安装jupyter 插件
在扩展中搜索`jupyter`, 只用安装只一个，其它的jupyter....会作为扩展一起安装。

- 现在打开任意以`.ipynp`后缀的文件，都会自动进入notebook的界面
- 右上角可以看到并选择我们在之前注册为内核的环境们