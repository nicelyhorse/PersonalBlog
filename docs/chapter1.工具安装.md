
# AI 小说生成器安装与使用指南（从零开始）

> 本文将手把手教你如何在本地安装并运行这款 **基于大语言模型的自动小说生成工具**。
> 工具的全部安装步骤来源于项目官方文档，并经过个人整理优化，适合作为入门教程。
>

---

## 1. 环境准备

开始之前，请确认你的计算机满足以下条件：

* **Python 3.9+（推荐 3.10–3.12）**本文中使用的python版本是3.10
* **pip** 包管理工具（确保在解释器中存在pip这个库可以作为命令调用）
* 有效的 LLM API 密钥：

  * 支持OpenAI/DeepSeek/其他兼容OpenAI接口的服务（需要联网）
  * 支持本地模型接口（无需网络）

---

## 2. 下载项目源代码

你可以使用Git下载项目，也可以从GitHub直接获取压缩包。

### ✔ 方法一：使用Git克隆仓库

打开IDE后（例如PyCharm/VSCode/云端环境，本文使用的是PyCharm）在终端输入以下命令

```bash
git clone https://github.com/YILING0013/AI_NovelGenerator
```
将项目代码克隆到本地/云端
### ✔ 方法二：直接下载ZIP

在 GitHub 项目主页点击 **“Download ZIP”** 解压后即可。

---

## 3. Windows用户可能需要安装C++构建工具（可选）

部分依赖库需要编译。如果安装过程中出现无法构建某些包的情况，可以安装：

**Visual Studio Build Tools**：
[https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/](https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/)
建议下载，本文已经下载

安装时务必勾选：

* ✔ **C++ 桌面开发**

（默认只安装 MSBuild，无法满足依赖编译需求）

---

## 4. 安装依赖

重新打开IDE的终端，先进入项目目录

```bash
cd AI_NovelGenerator
```

然后在终端通过pip工具安装配置文件中的依赖：

```bash
pip install -r requirements.txt
```

如果出现某些包漏装，可以手动补装：

```bash
pip install <包名>
```
### 在此步骤中可能出现的问题
![alt text](image.png)
==很可能是没有激活虚拟环境，在IDE中使用
```bash
source venv/Scripts/activate
```
激活环境，其中venv这个路径需要根据自己的虚拟环境创建的位置看情况修改==
---

## 5. 启动主程序（GUI）

安装完成后，运行主程序：

```bash
python main.py
```

这会启动图形化界面，你可以可视化地设置参数、生成设定、生成小说章节。

---

## 6. 基础配置说明（config.json）

程序会读取 `config.json` 用来配置模型、API Key与小说生成参数。
示例配置如下（来自README）：

```json
{
    "api_key": "sk-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "base_url": "https://api.openai.com/v1",
    "interface_format": "OpenAI",
    "model_name": "gpt-4o-mini",
    "temperature": 0.7,
    "max_tokens": 4096,
    "embedding_api_key": "sk-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "embedding_interface_format": "OpenAI",
    "embedding_url": "https://api.openai.com/v1",
    "embedding_model_name": "text-embedding-ada-002",
    "embedding_retrieval_k": 4,
    "topic": "示例主题",
    "genre": "玄幻",
    "num_chapters": 120,
    "word_number": 4000,
    "filepath": "D:/AI_NovelGenerator/filepath"
}
```

---

## 7. 本地模型用户（Ollama）注意事项

如果你使用本地模型作为 Embedding，启动命令为：

```bash
ollama serve
ollama pull nomic-embed-text
```

建议切换模型后手动清空项目 `vectorstore/` 文件夹，以避免旧向量影响剧情一致性。

---

## 8. 可选：打包为可执行文件（免Python环境）

如果你希望在没有Python的电脑上使用，可以通过PyInstaller打包：

```bash
pip install pyinstaller
pyinstaller main.spec
```

打包完成后，程序位于：

```
dist/main.exe
```

---

## 9. 安装完成后你可以做什么？

图形界面提供四步式操作：

1. **生成世界观与角色设定**
2. **生成完整章节目录（剧情蓝图）**
3. **生成章节草稿（自动检索设定）**
4. **章节定稿（更新角色状态与全局摘要）**

并提供一致性审校工具，适合写长篇小说的场景。

---

## 总结

现在你已经完成了：

✔ 下载项目
✔ 安装环境与依赖
✔ 配置模型与参数
✔ 启动 GUI 编辑器
✔ 可选：打包为EXE