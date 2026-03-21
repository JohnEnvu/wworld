# 本地部署步骤
1. 下载ollama，直接官网下
2. 下载模型，也是官网下
3. ollama使用
```bash
# 启动服务
ollama serve
# 确认模型
ollama list
# 测试是否能运行
ollama run qwen3.5:9b
# 其他指令看 ollama help

# ollama启动后默认监听
http://localhost:11434

```

<!-- # 怎么在trae用本地的api -->
<!-- 1. 拓展下载cline -->
<!-- 2. 大概原理图
```bash
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│   Trae      │ ───→ │   Cline     │ ───→ │   Ollama    │
│   (IDE)     │      │  (插件)     │      │  (本地服务)  │
└─────────────┘      └─────────────┘      └─────────────┘
                                                ↓
                                         ┌─────────────┐
                                         │  Qwen3.5    │
                                         │  (本地模型)  │
                                         └─────────────┘
``` -->
<!-- 3. cline配置
```bash
API Provider	OpenAI Compatible
Base URL	http://localhost:11434/v1
API Key	    ollama（随便填）
Model ID	qwen3.5:9b
``` -->

# 模型参数
qwen3.5:{参数量}-{量化/版本}








