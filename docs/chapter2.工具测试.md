
# AI小说生成器工具测试的步骤和可能遇到的问题

> 本文将手把手教你如何在实际使用**基于大语言模型的自动小说生成工具**之前测试工具的各个模块。
>

---

## 1. ![alt text](image-1.png) LLM Model Settings

GUI界面详解

点击**新增** → 创建新的模型配置
点击**重命名** → 修改当前配置名称
点击**删除** → 移除该配置
点击**保存** → 保存当前设置参数
**APIKey**：在 DeepSeek 官网或 OpenAI 官网生成对应的 API Key，复制到输入框中
**BaseURL**：找找官方文档的API使用，将相应的baseurl复制过来即可
示例：
| 服务        | Base URL                        |
| DeepSeek  | `https://api.deepseek.com/v1`   |
| OpenAI    | `https://api.openai.com/v1`     |
**接口格式**：根据API的来源进行选择，大多数有OpenAI的兼容
**模型名称**：指定你希望使用的 LLM 的名称，具体请从官方文档中寻找自己需要的模型名称
示例：deepseek-chat
deepseek-reasoner
gpt-4o-mini
qwen2.5-7b-instruct
**Temperature**：| Temperature | 输出特点              |
| ----------- | ----------------- |
| 0.0–0.3     | 严谨、确定性高，适合一致性检查   |
| 0.4–0.7     | 平衡创意与稳定性（小说生成推荐）  |
| 0.7–1.0     | 更丰富、有想象力，但风险是逻辑偏离 |
**Max Token**：控制单次生成文本的最大长度。
越大代表模型能一次生成更多内容。
设太小 → 文本生成被截断
设太大 → 调用成本上升、部分模型无法支持
**Timeout**：请求模型时的最大等待时间，避免长时间卡死。推荐网络正常：30–60秒，大模型 + 超长生成：300–600秒


==测试错误合辑==
| 错误提示               | 原因           |
| `401 Unauthorized` | Key填错/已失效 | 
| `Invalid API Key`  | 使用了错误平台的Key | 
| 请求无响应              | Key额度用完     | 
| `404 Not Found`   | Base URL 写错路径，如少了 `/v1`     |
| `ConnectionError` | 墙、代理、无网络、服务挂了               |
| 返回 HTML 页面        | Base URL 指向了错误网址（不是API入口） |
| 返回 `Invalid URL structure` | 接口格式和 Base URL 不匹配      | 
| 测试配置失败但Key 正常             | DeepSeek协议与OpenAI不兼容 | 
| `model_not_found` | 模型名称拼写错误或该服务商不存在该模型 |
| `403 Forbidden`   | API Key 无访问该模型的权限   |

---

## 2. ![alt text](image-2.png) Embedding Settings

**本文作者在此处使用的向量化的模型是来自阿里百炼的大模型**
1. 在百炼平台注册账号并登录之后在主账号创建一个api key（作者充了100元的api额度），将key复制到第一个文本框中
2. 在接口格式中选择OpenAI的格式因为百炼的api有openai兼容
3. 在Embedding Base URL中请按照自己使用的模型的文档中的baseurl的样式进行填写，如果和本文作者一样使用了百炼模型则使用
```url
https://dashscope.aliyuncs.com/compatible-mode/v1
```
4. Embedding Model Name选择自己想要尝试的大模型的正式名字，正如上一个section中提及的，从官方文档中寻找
5. Retrieval Top-K控制：系统从向量数据库中找出多少条“最相关的信息”提供给LLM作为上下文，具体来说就是K越大，模型参考的文档越多，剧情一致性越强，但相应的消耗的tokens就会变多，甚至过大会导致模型难以识别重点可以根据自己的情况进行调整

==报错情况：如果遇到openai.BadRequestError: Error code: 400 - {'error': {'message': '<400> InternalError.Algo.InvalidParameter: Value error, contents is neither str nor list of str.: input.contents', 'type': 'InvalidParameter', 'param': None, 'code': 'InvalidParameter'}, 'id': '9515af7c-c156-4a38-9c75-2724b2e63a37', 'request_id': '9515af7c-c156-4a38-9c75-2724b2e63a37'}这种类型的报错请按照以下步骤修复==
1.打开项目里的 embedding_adapters.py
2. 找到self._embedding = OpenAIEmbeddings那一段初始化代码
3.在参数里加上：check_embedding_ctx_length=False，形如
```python
self._embedding = OpenAIEmbeddings(
    api_key=api_key,
    base_url=base_url,
    model=model_name,
    check_embedding_ctx_length=False, 
)
```
4.保存文件，重启你的GUI，再点一次测试Embedding配置

## 3. ![alt text](image-3.png) Config Choose
此项中几乎都是各种任务的配置，需要说明的地方不多，大家请根据自己能调用的大模型api和大模型对于各种任务的表现合理配置

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