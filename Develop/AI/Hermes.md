---
source_title: Hermes
categories:
- AI
- Develop
last_modified: '2026-06-30T01:38:52Z'
---
<!-- template: Infobox_Software

,  name = Hermes Agent

,  developer = [Nous Research](#Nous_Research)

,  version = v0.17.0

,  date = June 2026

,  language = Python, TypeScript/React

,  os = Linux, macOS, Windows, Termux

,  genre = AI Agent Framework

,  license = [MIT License](#MIT_License)

,  website = [Official Website](https://hermes-agent.nousresearch.com/)

,  github = [GitHub Repository](https://github.com/NousResearch/hermes-agent)

 -->

Hermes Agent 是由 Nous Research 开发的开源自我改进型 AI 代理框架。它以独特的内置学习循环为核心，被誉为“会成长的 AI 代理”，专为开发者、自动化爱好者和需要持久智能助手的用户设计。

### 核心特性

#### 自我学习与改进
* **技能自动化创建**：从复杂任务经验中自动生成可复用技能
* **技能自我优化**：在使用过程中持续改进技能质量和效率
* **代理级记忆系统**：支持周期性提示、FTS5 全文搜索和长期记忆持久化

#### 多平台支持
- 跨平台对话接口：Telegram、Discord、Slack、WhatsApp、Signal、Email、CLI 等
- 灵活部署方式：本地、Docker、SSH、Singularity、Modal（无服务器）、Daytona 等

#### 强大的开发者工具
- 现代终端界面（TUI）：基于 React + Ink，支持多行编辑、斜杠命令、自动补全、历史记录、中断重定向
- MCP 集成：可连接任意 MCP 服务器扩展功能
- 40+ 内置工具：代码执行、网络搜索、浏览器自动化、图像生成、文件操作等

#### 高级功能
* **Cron 调度**：自然语言配置的定时任务
* **委托与并行化**：可生成隔离子代理并行处理复杂工作流
* **安全机制**：命令批准、容器隔离、DM 配对验证

### 项目架构

Hermes Agent 采用模块化设计，主要目录结构如下：
 ```
agent/                  # 核心代理循环、LLM 通信、工具系统
├─ model_metadata.py
├─ chat_completion_helpers.py
└─ transports/
hermes_cli/             # CLI 与 TUI 网关
├─ main.py
├─ models.py
├─ auth.py
└─ plugins.py
ui-tui/                 # React + Ink 终端 UI
├─ src/app.tsx
└─ src/components/
tools/                  # 40+ 工具实现
gateway/                # 消息平台网关
skills/                 # 核心技能库
providers/              # LLM 提供商支持（300+ 模型）
```

**运行流程**：*hermes* 命令启动 Python 后端（处理 LLM 调用、工具、存储），同时启动 TypeScript TUI 前端，两者通过 JSON-RPC over stdio 通信。

### 快速开始

#### 安装

**Linux / macOS / WSL2 / Termux**：
 ```
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
source ~/.bashrc
hermes
```

**Windows (PowerShell)**：
 ```
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
hermes
```

#### 首次配置
 ```
hermes setup              # 完整配置向导
hermes model              # 选择模型
hermes tools              # 工具配置
hermes gateway setup      # 配置消息平台（可选）
```

#### 基本使用
- `hermes` —— 启动交互式会话
- `/model <模型名>` —— 切换模型
- `/new` 或 `/clear` —— 新会话
- `/skills` —— 管理技能
- `/goal <目标>` —— 设置长期目标（Ralph 循环）
- `/cron add "每天上午9点..."` —— 创建定时任务

### 关键概念

#### Skills（技能）

Hermes 最具特色的功能。技能可从使用经验自动生成、可复用、跨会话持久，并能在使用中自我进化。支持社区分享和自定义开发。

#### Memory（记忆）
* **SOUL.md**：代理个性和背景
* **MEMORY.md**：长期记忆
* **USER.md**：用户偏好
- 会话记忆 + 智能压缩机制

#### Tools（工具）

内置 40+ 工具，覆盖代码执行、浏览器控制、网络搜索、图像生成、文件/数据库操作等。

### 技术栈

| 方面 | 技术 |
|:---|:---|
| 主语言 | Python 3.11+ |
| 后端 | FastAPI + Uvicorn |
| 前端 | React + Ink (TUI) |
| 数据库 | SQLite（FTS5 全文搜索） |
| 调度 | croniter |
| 消息平台 | python-telegram-bot、discord.py 等 |
| 包管理 | uv + setuptools |

### 从 OpenClaw 迁移
 ```
hermes claw migrate              # 自动迁移
hermes claw migrate --dry-run    # 预览
```

支持迁移个性文件、记忆、自定义技能、API 配置等。

### 文档与社区

| 资源 | 链接 |
|:---|:---|
| 官方文档 | https://hermes-agent.nousresearch.com/docs |
| Discord 社区 | https://discord.gg/NousResearch |
| Skills Hub | https://agentskills.io |
| GitHub | https://github.com/NousResearch/hermes-agent （20万+ 星标） |
| 主页 | https://hermes-agent.nousresearch.com/ |

### 核心优势
1. 真正的自主改进循环（其他代理难以实现的自学能力）
1. 极致灵活性（任意 LLM + 任意平台 + 任意工具）
1. 为开发者深度优化（强大 CLI/TUI + 完整代码执行）
1. 活跃社区驱动（20万+ 星标，庞大生态）
1. 生产就绪（Docker、多后端、安全机制完善）
