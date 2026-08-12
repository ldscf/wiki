---
source_title: Claude Code
categories:
- AI
- Develop
- Tools
last_modified: '2026-08-03T07:08:28Z'
---
Claude Code 是一个代理编码工具，可以读取代码库、编辑文件、运行命令。帮助构建功能、修复错误和自动化开发任务。能理解整个代码库、跨多个文件和工具工作以完成任务。可在终端、IDE、桌面应用和浏览器中使用。

以下是针对 macOS / Ubuntu 22.04，使用 [free-claude-code](https://github.com/Alishahryar1/free-claude-code) 开源项目，实现免费使用 Claude Code。

### 安装

#### 官网

2026/6 以后，可以直接命令安装，并启动一个 Web。macOS/Linux:
```
 curl -fsSL "https://github.com/Alishahryar1/free-claude-code/blob/main/scripts/install.sh?raw=1" | sh
```
 
```
 # Start Proxy, localhost, 8082
 fcc-server
```
 
```
 # Claude code
 fcc-claude
```
```
 # 配置文件
 ~/.fcc/.env
```

#### uv

uv 是快速的 Python 包管理器，项目强制使用。
```
 curl -LsSf https://astral.sh/uv/install.sh | sh
 -. OR .-
 brew install uv
```
 
```
 # 验证并更新
 uv --version
 uv self update     # 如果用 brew 安装，那么需要使用下面的命令。Homebrew 会接管该软件的文件路径和版本管理。
 # brew update && brew upgrade uv
```

#### Claude Code (CLI)
```
 curl -fsSL https://claude.ai/install.sh | bash
 -. OR .-
 npm install -g @anthropic-ai/claude-code
```
 
```
 # .zshrc
 # -> export PATH="$HOME/.local/bin:$PATH"
```
```
 # 验证
 claude --version
```
```
 # 升级
 官方原生脚本安装，会自动升级。下面命令可以显示当前的更新状态、版本号以及最近一次后台更新尝试的结果
 claude doctor
```

#### free-claude-code

Install Or Update
- macOS/Linux:
```
 curl -fsSL "https://raw.githubusercontent.com/Alishahryar1/free-claude-code/main/scripts/install.sh" | sh
```
 
```
 # 可能会出现安装 curl -fsSL https://chatgpt.com/codex/install.sh -o ... 报错
 1. 当前终端的所有子进程、包含 sh 内部的 curl 都走 1080 代理
```

  export ALL_PROXY="socks5h://127.0.0.1:1080"

  export http_proxy="socks5h://127.0.0.1:1080"

  export https_proxy="socks5h://127.0.0.1:1080"
```
 2. 重安装，但到 Pi 时，又会报错
```

  unset ALL_PROXY all_proxy
```
 3. 再重安装，会跳过前面到 Pi
```
 
```
 如果以前安装过旧版本，可能需要将 ~/.fcc/.env 删除/改名，否则会报变量赋值错误。(必须在本机访问，通过端口映射访问也报错！？)
```
 
```
 # 如果配置 agnes?
 agnes API 接口并未支持。于是可以：
 1. BEDROCK_BASE_URL : https://apihub.agnes-ai.com/v1
 2. AWS_BEARER_TOKEN_BEDROCK : sk-...
```
```
 -.OR.- 以下是很久以前的版本安装及配置
 git clone https://github.com/Alishahryar1/free-claude-code.git
 cd free-claude-code
 cp .env.example .env
```
 ```

# .env
NVIDIA_NIM_API_KEY="nvapi-..."                        # Key
MODEL_OPUS="nvidia_nim/deepseek-ai/deepseek-v4-pro"            # 强推理模型（Opus）：核心架构设计与疑难 Bug 排查
MODEL_SONNET="nvidia_nim/qwen/qwen3-coder-480b-a35b-instruct"  # 平衡模型（Sonnet）：日常编码的主力模型，兼顾速度与质量
MODEL_HAIKU="nvidia_nim/deepseek-ai/deepseek-v4-flash"         # 快速模型（Haiku）：简单的文件重命名、小范围重构及 Unit Test 生成
MODEL="nvidia_nim/minimaxai/minimax-m2.7"                      # 通用 fallback：备选 nvidia_nim/z-ai/glm4.7

# 访问鉴权
ANTHROPIC_AUTH_TOKEN="ldscf"                          # 自定义 token，与客户端保持一致

# Anthropic Key Sample:

# ANTHROPIC_AUTH_TOKEN="sk-ant-api03-L8p7Q2mR9nV4xWzB6cKjYtS1aD5fG3hI4uO0pE9rX2tY7uI1oP5aS8dF6gH4jK3lM2nB1vC9xZ0-AbCdEfGhIjKlMnOpQrStUvWxYz1234567890AB"

# 针对不同级别的模型参数（尚未明确）

# 注意：确保适配器代码会读取这些变量并将其合并到 API 请求的 extra_body 中
MODEL_OPUS_EXTRA_BODY='{"temperature": 0.5, "max_tokens": 16384}'
MODEL_SONNET_EXTRA_BODY='{"temperature": 0.3, "max_tokens": 16384}'
MODEL_HAIKU_EXTRA_BODY='{"temperature": 1.0, "max_tokens": 4096}'

# Thinking output

# Global switch for provider reasoning requests and Claude thinking blocks.

# Set false to suppress thinking across NIM, OpenRouter, LM Studio, and llama.cpp.
ENABLE_MODEL_THINKING=true

# 默认配置（不需要改）
ENABLE_THINKING=true                                  # 启用 thinking 标签显示
PROVIDER_RATE_LIMIT=40
PROVIDER_RATE_WINDOW=60
PROVIDER_MAX_CONCURRENCY=5                            # 并发控制，避免快速超限
```

#### 模型选择器
```
 brew install fzf
 # Ubuntu: apt install fzf
```
```
 # ~/.zshrc / ~/.bashrc
 # source ~/.zshrc
 alias claude-pick="/Users/ldscf/Documents/claude/free-claude-code/claude-pick"
 alias claude-free='ANTHROPIC_BASE_URL="http://localhost:18082" ANTHROPIC_AUTH_TOKEN="ldscf" claude'
```

#### VSCode Extension
1. VSCode: 安装 Claude Code for VS Code 扩展
1. Open Settings (Ctrl + ,) and search for claude-code.environmentVariables.
1. Click Edit in settings.json and add:
 ```
"claudeCode.environmentVariables": [
  { "name": "ANTHROPIC_BASE_URL", "value": "http://localhost:18082" },
  { "name": "ANTHROPIC_AUTH_TOKEN", "value": "ldscf" }
]
```

### Start

#### Proxy
```
 # screen -S Claude
 uv run uvicorn server:app --host 127.0.0.1 --port 18082
```

这个 free-claude-code 事实上是创建了一个 Anthropic 原生接口的 API 代理服务器，可以将 Claude Code 的请求格式转换并转发给 NVIDIA NIM 或其他后端。
 ```
curl http://localhost:18082/v1/messages \
  -H "Content-Type: application/json" \
  -H "x-api-key: ldscf" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-3-5-sonnet-20240620",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "你好"}]
  }'

# 对应的 Claude 模型
claude-3-opus-20240229
claude-3-5-sonnet-20240620
claude-3-haiku-20240307
http://localhost:18082/openapi.json         # 无法确定路径时看 openapi.json，"paths": 后面是调用路径
```

#### CLI

claude-free
 ```
 ▐▛███▜▌   Claude Code v2.1.119
▝▜█████▛▘  Sonnet 4.6 · API Usage Billing
  ▘▘ ▝▝    ~/github/free-claude-code
  Opus 4.7 xhigh is now available! · /model to switch
```

#### VSCode

使用类似于 Cline

### 模型兼容

#### DeepSeek
 ```

# 一般写在 .zshrc / .bashrc 中：
alias claude-ds='ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic" \
ANTHROPIC_AUTH_TOKEN="sk-354e477e737b45e1b44cb01afae30fca" \
ANTHROPIC_MODEL="deepseek-v4-pro[1m]" \
ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-v4-pro[1m]" \
ANTHROPIC_DEFAULT_SONNET_MODEL="deepseek-v4-pro[1m]" \
ANTHROPIC_DEFAULT_HAIKU_MODEL="deepseek-v4-flash" \
CLAUDE_CODE_SUBAGENT_MODEL="deepseek-v4-flash" \
CLAUDE_CODE_EFFORT_LEVEL="max" \
claude'
```

### 附录

**🛠️ Claude Code 状态解读**

| 关键词 | 核心状态 | 底层逻辑 | 预期耗时 |
|:---|:---|:---|:---|
| **Percolating** | 正在酝酿 | 正在解析 Prompt 意图，初始化本地上下文（Context）索引。 | 极短 |
| **Gusting** | 疾风模式 | **热启动响应**。正在处理已缓存或极度熟悉的上下文，读写速度极快。 | 极短 |
| **Baking** | 正在烘焙 | 回答生成的最后阶段，正在进行逻辑自检、语法校验或格式对齐。 | 极短 |
| **Brewing** | 正在发酵 | 逻辑推理中。处理中短篇幅的逻辑，如解答技术点或小规模代码修改。 | 短 |
| **Ideating** | 正在构思 | 涉及多文件关联、目录初始化（如 /init）或复杂架构设计的规划。 | 中 |
| **Churning** | 搅动处理 | **任务清理与重试**。上一个任务失败或超时（Timeout）后，正在清理残余进程并准备恢复现场。 | 中 |
| **Boogieing** | 正在输出 | **重头戏**。正在将思考结果转化为实际资产（代码、报告），准备执行磁盘 I/O。 | 中/长 |
| **Sautéed** | 火热煸炒 | 持续性的计算任务。通常出现在长时间的日志审计、多轮重构或深层依赖树爬取中。 | 中/长 |
| **Billowing** | 翻腾模式 | **海量数据处理**。涉及大规模文件扫描、巨量 Token 吞吐或跨模块的复杂关联计算。 | 长 |
| **Tomfoolering** | 深度博弈 | **异常或极复杂处理**。当主路径受阻（如 401 报错、路径冲突），正在尝试多种备选方案（Fallbacks）。 | 极长 |
| **Infusing** | 正在注入 | **IDE 通道传输**。正在尝试将数据/控制指令通过 RPC 通道推送到 VS Code 插件端。 | - |
| **Cultivating** | 正在耕耘 | **逻辑嫁接期**。通常出现在大型审计任务或跨模块重构的起始阶段。正在对多来源的信息进行整合、修剪和逻辑对齐。 | - |
