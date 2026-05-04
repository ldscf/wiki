---
source_title: Openclaw
categories:
- Develop
- Platform
last_modified: '2026-04-04T15:01:12Z'
---
[OpenClaw](https://github.com/openclaw/openclaw)是一个用 Node.js 构建的开源项目，允许在服务器上运行一个功能强大的个人 AI 助手。
- 多模型支持：可以同时接入本地模型（Ollama、llama.cpp 等）和云模型（Gemini、Claude、Groq、OpenAI、Moonshot、DeepSeek）
- 多通道接入：Telegram Bot、WhatsApp、Discord、Slack、Matrix、iMessage
- 工具/技能系统：内置和可扩展的工具，包括网页搜索、代码执行、图像生成、PDF 处理、GitHub 操作、语音合成
- 插件生态：支持自定义技能（skill）和自动化钩子（hook）

**使用场景**
- 在 Telegram 上随时问服务器状态、写脚本、重启服务
- 用 WhatsApp 让 AI 总结邮件、分析 PDF、生成周报
- 把 AI 加到 Discord 群，当技术支持 bot

[官网](https://openclaw.ai/)

### Inst
```
 # 全自动安装，包括依赖（如 Node、git）
 # macOS & Linux
 curl -fsSL https://openclaw.ai/install.sh | bash
```
```
 # Windows
 powershell -c "irm https://openclaw.ai/install.ps1 | iex"
```

或者：

#### 安装 Node.js
```
 apt update
 curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
 apt install nodejs
```
 
```
 # node -v
 # npm -v
```

#### git
```
 apt install git
```

#### openclaw
```
 npm install -g openclaw@latest
```

需安装 500+ 个包，用时约 4 分钟。

如果某个版本有问题，可以安装指定版本。如：OpenClaw 2026.3.22 的已知严重打包 bug（GitHub issue #52808）：npm 官方包漏掉了 dist/control-ui/，gateway 启动正常，网页 18789 端口访问报 “Control UI assets not found”错误。
```
 npm install -g openclaw@2026.3.13
```

### Channel

#### Telegram Bot

OpenClaw 对 Telegram 的支持最成熟，Telegram 提供免费、无限消息、无需自己服务器中转。需要自行创建 Bot。
1. 在 Telegram 查找 @BotFather
1. /newbot: username/username_bot
1. 获取 token
1. 在聊天窗口中，把任意消息转发给 @userinfobot 或 @getidsbot，它会立刻回复这个聊天的 chat_id。一般群组为 -100xxx

#### Discord Bot

Discord Bot 是免费创建的，OpenClaw 通过 Discord 的官方 Bot API 接入，支持私聊、服务器频道、mention 过滤、白名单等。

**创建 Server**
1. [Discord](https://discord.com/channels/@me)
1. 左侧 Add a Server → Create My Own

**获取 channel ID**
1. 开启 Developer Mode（用户设置 → 高级 → 开发者模式）
1. 在频道右键（如：#general） → Copy Channel ID

**创建 Bot**
1. [Discord Developer Portal](https://discord.com/developers/applications)
1. 右上角 New Application → Create
1. 左侧 Bot 标签 → Add Bot
1. Privileged Gateway Intents: 全选
1. 获取 Bot Token（以后不会再出现，忘记的话 Reset Token）

**将 Bot 加入 Discord 服务器**
1. 选中 My Applications 中 bot
1. 左侧菜单选 OAuth2 → OAuth2 URL Generator 选 bot
1. 在下面出现的 Bot Permissions 选：
  1. View Channels
  1. Send Messages
  1. Read Message History
  1. Embed Links（发图片/链接预览）
  1. Attach Files（发文件）
  1. Mention Everyone
1. 复制 Generated URL 中的邀请链接，在浏览器打开
1. 选择要加入的服务器（上面创建的）

### 配置
```
 openclaw onboard --install-daemon
```
1. 设置 Gateway（默认端口 18789，本地监听）
1. 配置模型（AI 大模型，如 Grok、Claude、Gemini 等，需要 API Key）  

Google Gemini API key: google/gemini-3.1-pro-preview
1. 配置通道（Telegram、WhatsApp、Discord 等，需要 token）
1. Configure skills：yes（默认，以下均为默认）  

Install missing skill: Skip
1. 配置完毕后，会给出 Web UI 访问地址和 token
1. TUI：终端交互界面（Text-based User Interface）是字符界面的管理面板，OpenClaw 官方最推荐的首次启动方式。
1. 批准 pairing policy（安全默认：需要手动 approve）  

openclaw pairing list  

openclaw pairing approve LJKWQ...

#### 浏览器交互

自动化插件

| 插件名称 | 描述 | 功能 |
|:---|:---|:---|
| OpenShell | Sandbox 容器环境 | 提供一个带有本地工作空间镜像的执行环境，可运行自动化脚本。 |
| Puppeteer/Playwright | 浏览器自动化 | 模拟浏览器行为，自动填入 KEY、点击搜索并提取结果。 |
| LLM Task | 结构化任务执行 | 如果该聊天服务提供 API 接口，此工具可处理复杂的 JSON 任务交互。 |
1. 启用 OpenShell 环境（用于运行自动化脚本）  

OpenClaw 内置了 OpenShell 插件，这是实现浏览器自动化脚本（如 Node.js 脚本）的基石。  

openclaw plugins enable openshell
1. 重启网关应用配置  

openclaw gateway restart
1. 安装自动化依赖（在 Workspace 中）  

cd /root/.openclaw/workspace && npm install playwright
1. 安装浏览器内核（Playwright 运行必需，好像不安装也可以）  

npx playwright install --with-deps

### 运维

openclaw 有两种定时任务：HEARTBEAT.md 依赖心跳间隔，cron add 创建真正的定时任务。

**任务管理**
 ```
ID=0056ac8a-88dc-495b-9603-2441839eb18c

# 任务管理
openclaw cron list --all                   # 显示所有（包括已禁用）
openclaw cron list --json                  # 原始 JSON 格式
openclaw cron runs --id $ID --limit 10     # 查看任务历史
openclaw cron rm $ID                       # 删除任务
openclaw cron run $ID                      # 手动触发测试

# 创建任务
  -> 转发至 discord/channel
  --session main / isolated：运行在主会话还是独立会话
TITLE="hourly-system-check"
MESSAGE="每小时检查系统资源占用（CPU 使用率、内存占用百分比、磁盘IO 和剩余空间）。生成清晰报告，如果 CPU>80% 或 内存>85% 或 磁盘剩余<20% 请用醒目方式突出异常。执行完成后必须使用 announce 模式将完整报告发送到 Discord #系统运维 频道。"
openclaw cron add \
  --name "${TITLE}" \
  --cron "0 * * * *" \
  --tz "Asia/Seoul" \
  --session isolated \
  --message "${MESSAGE}" \
  --announce \
  --channel discord \
  --to "channel:1485906388172669109"
  --best-effort-deliver

# 服务状态
 openclaw gateway status
 openclaw gateway restart
```

**Heartbeat**

Heartbeat（心跳）cron add 创建的定时任务是两种作用不同的机制。Heartbeat 是“定期巡检 + 按需报告”：每隔固定间隔（默认 30 分钟）自动唤醒。读取
```
 ~/.openclaw/workspace/HEARTBEAT.md
```

作为本次心跳的“检查清单”。如果有需要处理的事项 → 执行并可能输出报告（投递到聊天或配置的通道）。如果一切正常 → 通常只回复 HEARTBEAT_OK。

### Error

#### API rate limit reached
```
 # 当前默认模型
 openclaw config get agents.defaults.model
 # 更换
 openclaw models set google/gemini-3.1-flash-lite-preview
```

#### 网络搜索
```
 openclaw configure --section web
 # 选择：DuckDuckGo Search (experimental)
 # DuckDuckGo Search (experimental) works without an API key.
```
