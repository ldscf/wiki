---
source_title: Ollama
categories:
- AI
- Develop
last_modified: '2025-02-27T06:29:50Z'
---
Ollama 是为了快速部署 [[LLaMA]] 大模型而诞生的，目前在 https://ollama.com/library 列出了可以支持部署的 LLM。

### Ollama

#### Install
```
 curl -fsSL https://ollama.com/install.sh | sh
```

#### ENV
```
 export OLLAMA_HOST=127.0.0.1:30082    # 如果修改了默认端口(11434)
```

#### SETUP
1. OLLAMA_MODELS: 模型文件存放目录
1. OLLAMA_HOST: Ollama 服务监听的网络地址，默认为 127.0.0.1(0.0.0.0 允许外部网络访问)
1. OLLAMA_PORT: Ollama 服务监听的默认端口，默认为 11434(修改后需要在环境变量 OLLAMA_HOST 指定)
1. OLLAMA_ORIGINS: HTTP 客户端请求来源，半角逗号分隔列表，设置成星号，表示不受限制

#OLLAMA_KEEP_ALIVE: 大模型加载到内存中后的存活时间，默认为 300(s/m/h。0 = 处理请求响应后立即卸载模型，负数 = 一直存活）
1. OLLAMA_NUM_PARALLEL: 请求处理并发数量，默认为 1，即单并发串行处理请求，可根据实际情况进行调整
1. OLLAMA_MAX_QUEUE: 请求队列长度，默认值为 512，可以根据情况设置，超过队列长度请求被抛弃

#OLLAMA_DEBUG: 输出 Debug 日志标识，0/1 = 输出详细日志信息
1. OLLAMA_MAX_LOADED_MODELS: 同时加载到内存中模型的数量，默认为 1
```
 # 开放外部访问
 # /etc/systemd/system/ollama.service
 [Service]
 Environment="OLLAMA_HOST=0.0.0.0:30082"
```
 
```
 systemctl daemon-reload
 systemctl restart ollama
```
```
 *```
```

# 只能本地访问

Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    

tcp        0      0 127.0.0.1:11434         0.0.0.0:*               LISTEN      173310/ollama       

-->

# 允许外部网络访问

tcp6       0      0 :::11434                :::*                    LISTEN      173438/ollama       

# http://192.168.0.100:11434/

Ollama is running

```*

#### CMD
 ```
ollama pull llama3     # pull, llama3 default 8B
ollama run llama3      # run, if not exist & pull
ollama ps              # run model list
ollama list            # list images
ollama rm llama3:8b    # remove images
ollama cp ollama:8b ollama:latest      # copy model
>>> Downloading ollama...
  ######################################################################## 100.0%#=#=#                                              ######################################################################## 100.0%
  >>> Installing ollama to /usr/local/bin...
  >>> Creating ollama user...
  >>> Adding ollama user to render group...
  >>> Adding ollama user to video group...
  >>> Adding current user to ollama group...
  >>> Creating ollama systemd service...
  >>> Enabling and starting ollama service...
  Created symlink /etc/systemd/system/default.target.wants/ollama.service → /etc/systemd/system/ollama.service.
  >>> The Ollama API is now available at 127.0.0.1:11434.
  >>> Install complete. Run "ollama" from the command line.
  WARNING: No NVIDIA/AMD GPU detected. Ollama will run in CPU-only mode.
```

### MODEL

模型文件路径(Linux): /usr/share/ollama/.ollama/models
 ```
1. ollama list
NAME	ID	SIZE	MODIFIED 
llama3:8b	62757c860e01	4.7 GB	6 months ago
llama3.1:8b     a23da2a80395    4.7 GB  6 months ago
deepseek-r1:32b	38056bbcbb2d	19 GB 	12 minutes ago
1. ollama pull llama3
pulling manifest 
pulling 6a0746a1ec1a...  84% ▕███████████████████████████████████████████████████████           ▏ 3.9 GB/4.7 GB   39 MB/s     21s
...
pulling 6a0746a1ec1a... 100% ▕██████████████████████████████████████████████████████████████████▏ 4.7 GB                         
pulling 4fa551d4f938... 100% ▕██████████████████████████████████████████████████████████████████▏  12 KB                         
pulling 8ab4849b038c... 100% ▕██████████████████████████████████████████████████████████████████▏  254 B                         
pulling 577073ffcc6c... 100% ▕██████████████████████████████████████████████████████████████████▏  110 B                         
pulling 3f8eb4da87fa... 100% ▕██████████████████████████████████████████████████████████████████▏  485 B                         
verifying sha256 digest 
writing manifest 
removing any unused layers 
success
```

[[LLaMA]]

Llama-3:8b(占用存储 4.7G) 可在一台 6T CPU/12G RAM 主机上运行，单个 chat 会将全部 CPU 跑满，内存占用 0.5G。而此环境所能使用的最大模型是 llava:13b，这是一个 8G 的模型，使用中内存占用约 2G。llama3.1:70b(39G): Error: llama runner process has terminated: signal: aborted (core dumped) 

[[DeepSeek]]

deepseek-r1:14b(占用存储 9.0G) 可在一台 6T CPU/16G RAM 主机上运行，内存占用 9 G 左右。

deepseek-r1:32b model requires more system memory (21.5 GiB) than is available.

#### Instructions
```
 curl http://192.168.0.100:11434/api/generate -d '{
```

   "model": "llama3",

   "prompt": "Why is the sky blue?",

   "stream": false,

   "options": {

     "num_ctx": 4096

   }
```
 }'
```

参数 

*model：（必填）模型名称

*messages：聊天的消息，这可以用来保持聊天记忆，具有以下字段：

**role：消息的角色，可以是 system、user 或 assistant

**content：消息的内容

**images（可选）：要包含在消息中的图像列表（适用于多模态模型，如 llava）

高级参数（可选）：

*format：返回响应的格式。目前唯一接受的值是json

*options：文档中列出的额外模型参数 Modelfile，如 temperature

*stream：如果为 false，则响应将作为单个响应对象返回，而不是对象流

*keep_alive：控制模型在请求后保持在内存中的时间（默认：5m）

#### 模型文件格式

#GGUF(GPT-Generated Unified Format)是一种可扩展的二进制模型文件格式，是 GGML、GGMF 和 GGJT 的后继文件格式，可以在不破坏兼容性的情况下将新信息添加到模型中。GGUF 支持模型量化，可以将模型权重量化为较低位数的整数，降低模型大小和内存消耗，提高计算效率，同时平衡性能和精度。Quant method(浮点数的位数和量化的方式)，如 Q3 表示 3 位量化

#Safetensors 是 HuggingFace推 出的一种新的模型存储格式。与传统的模型存储格式相比，Safetensors不包含执行代码，因此在加载模型时无需进行反序列化操作，从而实现更快的加载速度。Safetensors 只包含模型的权重参数，而执行代码则由加载模型的框架或库提供。

### UI

#### ChatBox

Chatbox AI 是一款 AI 客户端应用和智能助手，支持众多先进的 AI 模型和 API，可在 Windows、MacOS、Android、iOS、Linux 和网页版上使用。

*[ChatBox Desktop Download](https://chatboxai.app/)

*[ChatBox online](https://web.chatboxai.app/)

#### open-webui

Open WebUI is an extensible, feature-rich, and user-friendly self-hosted WebUI designed to operate entirely offline. It supports various LLM runners, including Ollama and OpenAI-compatible APIs.

*[open-webui github](https://github.com/open-webui/open-webui)

Start with Docker

*Ollama is on your computer
```
 docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

*Ollama is on a Different Server
```
 docker run -d -p 3000:8080 -e OLLAMA_BASE_URL=http://192.168.0.249:11434 -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

Upgrade

# docker ps

# docker stop 

# docker rm 

# docker rmi ghcr.io/open-webui/open-webui:main

# docker run ...

提供 RAG 功能：

设置 - 个性化 - 记忆 (实验性)：通过点击下方的“管理”按钮，你可以添加记忆，以个性化大语言模型的互动，使其更有用，更符合你的需求。
```
 --> User 是一个 Java、Python、C、C++、SQL 软件开发人员。
```

事实上，通过发给 LLM 的信息中包含了这部分信息：
```
 "userContext": "1. [2024-08-05]. User 是一个 Java、Python、C、C++、SQL 软件开发人员。\n",
```

## 参考

#[在 Linux 上安装 Ollama](https://ollama.fan/getting-started/linux/)
