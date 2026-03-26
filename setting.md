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
# git配置
1. 配置git用户名和邮箱
```bash
git config --global user.name "JohnEnvu"
git config --global user.email "2269733646@qq.com"
# 开始照着git新建仓库上的流程进行就行
# # 2. 添加远程仓库（SSH 格式）
git remote add origin git@github.com:JohnEnvu/wworld.git
# http方式
# git remote add origin https://github.com/JohnEnvu/wworld.git

# 复述一遍配置流程吧，还是要知道流程在做什么的
# 妈上班的时候就被李俊哥拷打了哈哈，没有张泉那么温柔
# 1. 初始化仓库（如果还没有）
git init
# 2. 添加远程仓库（SSH 格式）
git remote add origin git@github.com:JohnEnvu/wworld.git
# 3. 添加文件
git add .
# 4. 提交
git commit -m "Initial commit"
# 5. 首次推送
git push -u origin main
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
<!-- # 模型参数 -->
<!-- qwen3.5:{参数量}-{量化/版本} -->







