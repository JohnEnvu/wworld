# 资料
1. 魔塔
https://modelscope.cn/models/ZhipuAI/GLM-5/files
2. 模型社区
https://blog.csdn.net/qq_57220546/article/details/159006465?ops_request_misc=&request_id=&biz_id=102&utm_term=ollama%E6%9C%AC%E5%9C%B0%E9%83%A8%E7%BD%B2&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-159006465.142^v102^pc_search_result_base9&spm=1018.2226.3001.4187
3. dify，有点意思 - 晚点看看
https://cloud.dify.ai/apps
4. Continue（VS Code 插件）
安装 VS Code
安装 Continue 插件
配置本地模型（Ollama）
在 IDE 内打开侧边栏聊天
输入 @文件名 引用文件
发送到本地大模型
显示回答
5. 手搓
```bash
前端（界面）：
- Electron（跨平台桌面应用）
- 或：Tauri（更轻量）
- 或：纯 Web 应用（浏览器访问）
后端（服务）：
- Python + FastAPI
- 或：Node.js + Express
向量数据库：
- Chroma（简单）
- 或：Milvus（强大）
大模型：
- Ollama + Qwen2.5
- 或：vLLM + Qwen2.5
RAG 框架：
- LangChain
- 或：LlamaIndex
```

# 解惑
1. ollama部署模型，调用是有一个固定的端口的
http://localhost:11434
2. rag：先检索后生成
3. agent：大模型是大脑，agent就是大模型可以调用的工具，比如操作文件
agent能用工具，能获取到更多的信息，如搜索文件，调用api，读写文件等
这么说rag其实是依赖于agent的？
记忆功能也是基于agent的，但是我不知道具体是怎么记忆的
工作模式：
react：思考 → 行动 → 观察 → 思考 → 行动 → ... → 回答
Plan-and-Solve 模式：制定完整计划 - 逐步执行计划 -  汇总结果
<!-- 架构：... -->
4. mcp：大模型和外界工具交互的协议 - 这里的工具不是传统的工具吧，应该是其他项目模块
同一接口
<!-- 5. Prompt Engineering（提示词工程） -->
## 5. 上下文长度 = 模型的工作记忆容量
<!-- 就像人的短期记忆，一次性能记住的信息量有限 -->

超出上下文长度的记忆内容，先进先出丢弃
agent可以向量数据库存储长期记忆，就像是堆一样，短期记忆就放在栈上（感觉上类似吧）
所以现在的重点是这个向量数据库是不是本地存储的，独立的对话之间能否相互调用？这个向量数据库能不能做到成为我的知识库的要求？生命周期是多长？

记忆存储方式：
全部保存在上下文 - 类似全部放在栈上
在第一种方式的基础上提取关键词，压缩上下文用量
用rag调用外部记忆（向量数据库），使用时检索 - 最理想的方式

总内容远超上下文长度的场景怎么解决：
分层记忆：
<!-- 我想知道分层记忆系统能全部保存在向量数据库中吗？我担心一件事，我要是让当前的上下文记录了大纲，然后逐层向深处调用，如我要用到第一章的知识，上下文保存了大纲，然后去深层调用第一张保存的精炼概述，然后去调用更深层存放的原文，这不是每次都要告诉大模型第一章大纲吗？而且有上下文溢出的风险，我想能不能就说让大模型去看第一章，然后就顺着链路检索，但是关于第一章的所有内容：原文，精炼语，概述（由详细到粗略）的内容都存在向量数据库中，不会丢失，简单来说就是我不希望丢失记忆，并且用极小量的关键词就能调用对应的记忆，能做到吗？ -->
问题：
1. 每次都要把"大纲→概述→原文"的链路重复放入上下文
2. 占用宝贵的上下文空间
3. 容易溢出
4. 检索效率低
理想状态：
- 所有内容（大纲、概述、原文）都存储在向量数据库
- 用极少的关键词就能触发检索
- 自动沿着"大纲→概述→原文"的链路检索
- 不丢失任何记忆
- 不占用上下文空间
<!-- 解决方案：不再在上下文中保存层级结构，而是将层级关系作为元数据存储在向量数据库中
优化方式：
[向量数据库] 自动存储所有层级 + 元数据索引
[检索] 智能识别层级，自动获取相关内容 -->
```bash
# 每个文档片段的存储结构
{
    # 内容
    "text": "第一章：RAG 技术原理...",
    
    # 向量（用于相似度搜索）
    "vector": [0.123, -0.456, ...],
    
    # 元数据（用于层级检索）
    "metadata": {
        "type": "chapter_content",      # 内容类型：outline/chapter_summary/chapter_content
        "paper_id": "paper_001",        # 论文 ID
        "chapter_id": "chapter_01",     # 章节 ID
        "level": 2,                     # 层级：0=大纲，1=章节摘要，2=原文
        "section": "2.1",               # 小节编号
        "parent": "outline",            # 父节点
        "children": ["2.1.1", "2.1.2"], # 子节点
        "keywords": ["RAG", "检索"],     # 关键词索引
        "created_at": "2024-01-01",     # 创建时间
        "word_count": 3000              # 字数
    }
}
```
<!-- ## 怎么搭建rag记忆体系 -->
# 任务表
1. 简单总结一下我的需求：
为什么要搭建：trae中的原生大模型需要排队，体验不好+本地部署数据更自由，还能搭建自己的知识库，trae中只是一次性的

要一个本地部署模型作为大脑 - 主要是免费+不用排队

想要有近似于trae的体验：
    在trae中接入本地模型，只替换大脑，继承其他所有的便利功能
    用dify
    或vscode插件continue - 这个引用文件比较麻烦，不知道rag能不能弥补，我引用文件或者文段内容都是为了针对性提供检索数据
        这里有疑问：我想要在vscode中也达到这种方式，如：是要把一整段的参考文本都送入对话框吗？能不能做到用更少的词就能引用到对应的记忆？或者只输入一次，就能记到记忆中，这样也行。达到的效果是我在后续使用时能精准的回忆起这个参考文本的内容。
    手搓 - 我觉得不是不可行，但是ui怎么办

2. 新思路，在trae中配置rag，然后购买第三方的api避免等待

3. 使用反向代理

3.1 问题
1. Trae 只支持调用云端 API（如 OpenAI）
2. Trae 不支持直接配置本地模型（如 Ollama）

3.2 解决方案
使用反向代理"欺骗"Trae
1. 让 Trae 以为在调用云端 API
2. 实际请求被转发到本地 Ollama

传统方式（不支持本地）：
┌─────────────────────────────────────────────────────────┐
│  Trae → https://api.openai.com → OpenAI 模型            │
└─────────────────────────────────────────────────────────┘

反向代理方式（支持本地）：
┌─────────────────────────────────────────────────────────┐
│  Trae → https://api.x.ai → Caddy 代理 → Ollama → 本地模型│
│         ↑                        ↑                      │
│         假装是云端 API          转发请求                │
└─────────────────────────────────────────────────────────┘

3.3 为什么需要 Caddy？

问题 1：协议不匹配
```bash
# Trae 期望：HTTPS 的 OpenAI API
# Ollama 提供：HTTP 的本地服务
# 解决：Caddy 在中间转换
Trae → HTTPS → Caddy → HTTP → Ollama
```

问题 2：域名验证
```bash
# Trae 要求：合法的 API 域名
# Ollama 只有：localhost:11434
# 解决：Caddy 创建本地域名
api.x.ai → hosts 文件指向 127.0.0.1
```

问题 3：Host 头问题
```bash
# Trae 发送：Host: api.x.ai
# Ollama 期望：Host: localhost:11434
# 解决：Caddy 重写 Host 头
header_up Host localhost:11434
```
4. 配置流程详解

4.1 安装 Ollama 并下载模型
```bash
# 1. 安装 Ollama（Windows 访问 https://ollama.ai 下载安装）
# macOS 用 Homebrew:
brew install ollama

# 2. 启动服务
ollama serve

# 3. 下载模型（推荐 Qwen2.5-Coder）
ollama pull qwen2.5-coder:7b

# 4. 验证
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-coder:7b",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

4.2 安装 Caddy
```bash
# macOS
brew install caddy

# Windows
# 访问 https://caddyserver.com/download
# 下载 Windows 版本
```

4.3 配置本地域名

编辑 hosts 文件：

Windows (PowerShell 管理员):
```powershell
notepad C:\Windows\System32\drivers\etc\hosts
```

macOS/Linux:
```bash
sudo nano /etc/hosts
```

添加一行：
```
127.0.0.1 api.x.ai
```

4.4 创建 Caddy 配置文件

创建 Caddyfile（无扩展名）：
```bash
mkdir -p ~/caddy
cd ~/caddy
nano Caddyfile
```

写入配置：
```
api.x.ai {
    tls internal
    reverse_proxy localhost:11434 {
        header_up Host localhost:11434
    }
}
```

配置解释：
1. tls internal
   - 使用 Caddy 内置 CA 生成自签名证书
   - 避免证书错误警告
2. reverse_proxy localhost:11434
   - 将请求转发到 Ollama
3. header_up Host localhost:11434
   - 关键！重写 Host 头
   - 避免 Ollama 返回 403 错误

4.5 信任自签名证书
```bash
# macOS
caddy trust

# Windows
# 需要手动导入证书到信任的根证书颁发机构
```

4.6 启动 Caddy
```bash
# 方式 1：前台运行（能看到日志）
caddy run

# 方式 2：后台运行
caddy start
```

验证代理是否工作：
```bash
curl -v https://api.x.ai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-coder:7b",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

4.7 配置 Trae
1. 打开 Trae
2. 进入设置（Settings）
3. 找到 API 配置
4. 选择"自定义 API"或"OpenAI Compatible"
5. 填写：
   - API 地址：https://api.x.ai/v1
   - API Key：sk-no-key-required（任意值）
   - 模型名称：qwen2.5-coder:7b
6. 保存
5. 完整工作流程

用户操作：在 Trae 中提问"写个快速排序"

步骤 1: Trae 发送请求
┌─────────────────────────────────────────────────────────┐
│ POST https://api.x.ai/v1/chat/completions              │
│ Host: api.x.ai                                          │
│ Authorization: Bearer sk-no-key-required               │
│ Body: {"model": "qwen2.5-coder:7b", ...}               │
└─────────────────────────────────────────────────────────┘
                     ↓
步骤 2: DNS 解析
┌─────────────────────────────────────────────────────────┐
│ api.x.ai → 127.0.0.1 (hosts 文件)                        │
└─────────────────────────────────────────────────────────┘
                     ↓
步骤 3: Caddy 接收 HTTPS 请求
┌─────────────────────────────────────────────────────────┐
│ - 验证 SSL 证书                                          │
│ - 重写 Host 头：api.x.ai → localhost:11434              │
└─────────────────────────────────────────────────────────┘
                     ↓
步骤 4: 转发到 Ollama
┌─────────────────────────────────────────────────────────┐
│ POST http://localhost:11434/v1/chat/completions        │
│ Host: localhost:11434                                   │
└─────────────────────────────────────────────────────────┘
                     ↓
步骤 5: Ollama 处理请求
┌─────────────────────────────────────────────────────────┐
│ - 加载 qwen2.5-coder:7b 模型                            │
│ - 生成代码                                              │
└─────────────────────────────────────────────────────────┘
                     ↓
步骤 6: 返回响应
┌─────────────────────────────────────────────────────────┐
│ Ollama → Caddy → Trae → 显示给你                        │
└─────────────────────────────────────────────────────────┘

6. 这个方案的意义

6.1 解决了什么痛点

之前：
- Trae = 只能调用云端 API
- 需要排队
- 数据上传云端
- 按量付费

现在：
- Trae + Ollama + Caddy = 本地模型
- 无需排队
- 数据本地
- 免费使用

6.2 优点
1. 保留 Trae 的所有功能
   - 文件拖拽
   - 自动上下文
   - 代码补全
   - 流畅 UI
2. 使用本地模型
   - 免费
   - 无需排队
   - 隐私安全
3. 配置相对简单
   - 只需 3 个组件
   - 配置文件中文化
   - 有详细教程

6.3 局限性
1. 模型能力受限
   - 本地模型（7B-72B）< 云端最强模型
   - 上下文长度受限（通常 4K-32K）
2. 需要硬件支持
   - 至少 8GB 显存（7B 模型）
   - 推理速度取决于显卡
3. 没有 RAG 知识库
   - 这是关键！
   - 只能使用模型训练时的知识
   - 不能记住你的项目文档
7. 和你需求的匹配度

7.1 这个方案能解决什么

1. 不用排队 - 能解决 - 本地模型，无需等待
2. 免费使用 - 能解决 - 一次性硬件投入
3. Trae 体验 - 能解决 - 就是 Trae 本身
4. 文件拖拽 - 能解决 - Trae 原生功能
5. 代码补全 - 能解决 - Trae 原生功能
6. 私有知识库 - 不能解决 - 这个方案没有 RAG
7. 长期记忆 - 不能解决 - 无法记住项目文档
8. 精准检索 - 不能解决 - 没有向量检索

7.2 关键缺失

这个方案只是"替换大脑"，没有"记忆系统"

场景：你上传了项目私有协议文档

Trae + Ollama + Caddy:
- 无法读取你的文档
- 无法记住私有协议
- 每次都要重新说明

你需要：
- 上传文档到知识库
- 自动检索相关内容
- 基于私有知识回答
8. 最佳方案：Trae + Ollama + Dify

8.1 组合思路

```
Trae + Caddy + Ollama  → 解决日常编码（代码补全、快速问答）
           ↓
       配合使用
           ↓
     Dify（知识库）    → 解决知识查询（项目文档、私有协议）
```

8.2 使用策略

场景 1：写代码、代码补全
- 使用 Trae（配置本地 Ollama）
- 快速、流畅、无需排队

场景 2：查询项目知识
- 使用 Dify（浏览器访问）
- 精准检索私有文档

场景 3：复杂问题分析
- 先在 Dify 中查询文档
- 再在 Trae 中实现代码

8.3 工作流程示例

任务：实现设备登录功能

步骤 1：查询私有协议（Dify）
- 浏览器访问 Dify
- 问："设备登录的消息格式"
- Dify 检索私有协议文档
- 回答：{"msg_id": 0x0101, "device_id": "..."}

步骤 2：实现代码（Trae）
- 在 Trae 中打开代码文件
- 问："帮我实现登录函数，消息格式是..."
- Trae（Ollama）生成代码

步骤 3：调试优化
- 在 Trae 中继续优化代码
9. Windows 配置注意事项

文章是 macOS 的，Windows 需要注意：

9.1 安装差异
```bash
# macOS 用 Homebrew
brew install ollama
brew install caddy

# Windows
# Ollama: 下载 .exe 安装包
# Caddy: 下载 .zip 解压
# hosts 文件：C:\Windows\System32\drivers\etc\hosts
```

9.2 证书信任（Windows）
```powershell
# Windows 需要手动导入证书
# 1. 找到 Caddy 生成的证书
# 2. 双击安装
# 3. 选择"受信任的根证书颁发机构"
```

9.3 后台运行
```powershell
# Windows 后台运行 Caddy
Start-Process caddy.exe -ArgumentList "run" -WindowStyle Hidden
```

10. 总结

10.1 文章价值

这篇文章解决了：
1. Trae 不能用本地模型的遗憾
2. 通过反向代理实现"欺骗"
3. 保留 Trae 所有便利功能

但这个方案不完整：
1. 只有"大脑"（本地模型）
2. 没有"记忆"（知识库）
3. 无法解决你的全部需求

10.2 你的完整方案

```
完整方案 = Trae（本地模型）+ Dify（知识库）+ Continue（备选）

Trae + Ollama + Caddy:
- 日常编码
- 代码补全
- 快速问答

Dify（本地部署）:
- 知识库管理
- 文档检索
- 长期记忆

Continue（备选）:
- 作为 Trae 的补充
- 快速查询代码
```

10.3 行动建议

1. 第 1 步：部署 Ollama（10 分钟）
   - 下载模型 qwen2.5-coder:7b

2. 第 2 步：配置 Trae + Caddy（30 分钟）
   - 按照文章配置反向代理
   - 在 Trae 中使用本地模型

3. 第 3 步：部署 Dify（30 分钟）
   - 创建知识库
   - 上传项目文档

4. 第 4 步：组合使用
   - 编码用 Trae
   - 查文档用 Dify

你想现在就开始配置吗？我可以提供详细的 Windows 配置步骤。

参考链接：
https://blog.leon.kylins.tech/trae-%E9%85%8D%E7%BD%AE%E4%BD%BF%E7%94%A8%E6%9C%AC%E5%9C%B0%E5%A4%A7%E6%A8%A1%E5%9E%8B%E6%9B%B2%E7%BA%BF%E6%96%B9%E6%A1%88/
```