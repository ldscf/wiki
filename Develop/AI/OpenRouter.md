---
source_title: OpenRouter
categories:
- AI
- Develop
last_modified: '2026-02-25T07:10:19Z'
---
OpenRouter 是一个统一的 API 服务平台，旨在将各种大型语言模型（LLMs）和服务集成到一个统一的接口中。它允许用户通过简单的配置和调用，访问多个预训练的大模型，而无需自己部署和维护这些模型。
1. 支持多种预训练模型，如 OpenAI 的 GPT-4、Claude、Gemini 等
1. 提供部分免费开源模型，如 google/gemini-2.0-pro-exp-02-05:free

支持 Google 帐号验证登录。

### [OpenRouter](https://openrouter.ai)

#### API Key
1. 点击 Create API Key，创建 API（credit limit 不填写，表示无限制使用。如果是付费的模型，需要填写）
1. 生成 Key，只显示一次，注意保存

#### 额度限制

https://docs.together.ai/docs/rate-limits

| 账户状态 | 每分钟限制 (RPM) | 每日请求上限 (RPD) |
| 未充值的用户 (Tier 0) | 20 RPM | 50 次请求/天 |
| 已充值 ≥ $10 的用户 | 20 RPM | 1000 次请求/天 |
省钱方案：OpenRouter 上的 DeepSeek R1 或 Claude 3.5 Haiku 价格极其低廉（几块钱能写上万行代码）。用付费模型，[[VSCode#Cline|Cline]] 运行起来会像“丝滑的自动驾驶”，而用免费模型则像“走走停停的烂车”。

#### Free Model

有些模型需要设置 [Privacy Setting](https://openrouter.ai/settings/privacy)
```
 Privacy Settings -> Enable free endpoints that may publish prompts
```

以下为 2026/1/6 模型

##### OpenAI: gpt-oss-120b (free)

推理（Reasoning）能力非常强，在很多编码和数学测试中，表现接近 OpenAI o4-mini 甚至 GPT-4o。

##### Meta: Llama 3.1 405B / Hermes 3 405B (free)

非常适合处理复杂的 Rust 借用检查问题或 Java 架构设计，推理能力接近 GPT-4o。

##### Meta: Llama 3.3 70B Instruct (free)

HumanEval（编程测试）得分极高，在处理 Java Spring Boot 这种标准框架代码时，比 Llama 3.1 405B 更快且同样准确。

##### Google: Gemini 2.0 Flash Experimental (free)

适合用来做“代码扫描”，拥有 100 万（1M）的超长上下文，能一次性读取整个项目。

#### Chat

点击搜索框，在下拉列表中选择 free 模型。如：Google: Gemini Pro 2.0 Experimental (free)，在其他工具中使用 API Key 时，输入的名称是第二行内容（如：google/gemini-2.0-pro-exp-02-05:free），第三行是发布时间/内容大小限制及费用（如：Created Feb 5, 2025 - 2,000,000 context - $0/M input tokens - $0/M output tokens）。点击第一行名称后面的 Chat，即可使用。

### Tools

不建议使用这些工具，因为难免 Key 会被他人使用（目前未发现）。

#### [ChatBox](https://chatboxai.app)

ChatBox 开源多平台 AI 客户端，支持 Windows、macOS 和 Linux，以及 IOS、Android。

设置 -> 模型
1. 添加自定义提供方
1. OpenAI API 兼容
1. API 域名：https://openrouter.ai/api/v1/
1. 路径：/chat/completions
1. API 密钥：sk-or-v1-db98...
1. 模型：deepseek/deepseek-r1-distill-llama-70b:free、google/gemini-2.0-pro-exp-02-05:free

目前版本，如：1.9.8 版本，支持每个对话设置“特定模型”，如 A 对话使用 deepseek/deepseek-r1:free，B 对话 使用 gemini。(对话标题名称右侧...配置)

#### [Cherry Studio](https://cherry-ai.com)

Cherry Studio 国产多模型 AI 客户端，支持 Windows、macOS 和 Linux 系统。它集成了多种主流的大型语言模型（LLMs），如 OpenAI、Gemini 等，以及本地模型运行功能，用户可以根据需求自由切换云端和本地模型。
1. 点击设置按钮，找到 OpenRouter，输入 API 密钥，不用检查
1. 点击添加模型，deepseek/deepseek-r1-distill-llama-70b:free

#### [Dify](https://dify.ai)

Dify 是一个开源的 LLM 应用开发平台，它通过 HTTP 提供直观的可视化界面，帮助开发者快速构建和部署 AI 应用，支持包括模型管理、知识库、工作流编排等全方位功能。

### rate limit

2025/4/8: People wanted more access to free models than the 200 requests per day we allowed, so we now allow you to go higher (1000) if you add credits. Usage won't deplete, though. They are still free. This helps us mitigate abuse.
1. Free usage limits: If you're using a free model variant (with an ID ending in : free ), you can make up to 20 requests per minute.
1. If your account has less than 10 credits, you're limited to 50 requests per day.
1. If you maintain a balance of at least $10, your daily limit increases to 1000 requests per dav.
