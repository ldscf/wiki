---
source_title: AI Chat Demo
categories:
- AI
- Develop
- Python
last_modified: '2026-02-05T03:08:34Z'
---
实现了一个轻量级的网页版 AI 聊天应用，支持用户输入文本问题、上传小型文本文件，并通过 Google 免费模型生成流式响应。
1. 前端：纯 HTML + CSS + JavaScript，实现聊天界面、文件上传预览、Markdown 渲染以及流式接收响应。
1. 后端：基于 FastAPI，提供单一 /api/chat 流式接口，使用 Google Generative AI SDK（v2.0 异步版）调用 Gemini 模型，支持自动续写以避免输出被截断。

整体架构简单、无数据库、无会话状态，适合个人或小范围部署。

项目仓库：

https://github.com/ldscfe/ai-chat-assistant

### 系统架构
 ```
浏览器
   ↓ POST /api/chat
FastAPI
   ↓ 异步流式生成
Google GenAI API
   ↓ 逐块返回文本
FastAPI → 浏览器
   ↓ 实时解析 & 渲染 (marked.js)
浏览器
```

### 前端

#### 文件上传与限制
1. 限制文件大小为 15KB。
1. 使用 readAsText 读取纯文本，支持多种代码/文本扩展名。
 ```
fileInput.onchange = function() {
    const file = this.files[0];
    if (file.size > 15 * 1000) {
        alert("文件太大！请限制在 15KB 以内...");
        this.value = "";
        return;
    }
    const reader = new FileReader();
    reader.onload = function(e) {
        uploadedFileContent = e.target.result;
        uploadedFileName = file.name;
        // 更新提示区
    };
    reader.readAsText(file);
};
```

#### 流式接收与 Markdown 实时渲染

标准的 Web Stream API:
1. 后端返回 chunked。
1. 使用 ReadableStream 逐块读取响应。
1. 实时通过 marked.parse 渲染为 HTML，支持 Markdown 代码块显示。
1. 自动滚动到底部。
 ```
const reader = res.body.getReader();
const decoder = new TextDecoder();
let buffer = '';
while (true) {
    const { value, done } = await reader.read();
    if (done) break;
    buffer += decoder.decode(value, { stream: true });
    aiDiv.innerHTML = marked.parse(buffer);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
}
```

### 后端

主要技术栈
- FastAPI：高性能异步 Web 框架，提供流式响应。
- Google Generative AI SDK v2.0（`google.genai`）：异步客户端，支持原生流式生成。
- Pydantic：请求体验证。
- python-dotenv：加载环境变量。
- Uvicorn：ASGI 服务器。

#### 环境变量
 ```
from dotenv import load_dotenv
load_dotenv()
LLM_MODEL = os.getenv("LLM_MODEL")
API_KEY = os.getenv("AI_API_KEY")
```

#### 异步客户端

Lifespan 管理：FastAPI 官方推荐方式。使用 @asynccontextmanager 管理 AI 客户端的生命周期。在服务启动时初始化 genai.Client，在关闭时安全释放资源，确保服务的健壮性。
 ```
@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.client = genai.Client(api_key=API_KEY, http_options={'api_version': 'v1alpha'})
    yield
app = FastAPI(lifespan=lifespan)
```

#### 核心推理引擎

非阻塞 I/O：利用 asyncio 异步处理请求，在高并发场景下不会因为 AI 的推理耗时而导致服务器卡死。
 ```
async def gemma_once(prompt: str):
    response = await app.state.client.aio.models.generate_content_stream(
        model=LLM_MODEL,
        contents=prompt,
        config=types.GenerateContentConfig(temperature=0.7, max_output_tokens=4096)
    )
    async for chunk in response:
        if chunk.text:
            yield chunk.text
```

#### 流式响应
 ```
return StreamingResponse(
    full_stream(request.message),
    media_type="text/plain",
    headers={"X-Accel-Buffering": "no"}
)
```

#### 中断检测

通过正则表达式检查 AI 输出是否意外中断（如代码块未闭合、句子未结束）。
 ```
def needs_continue(text: str):
    if text.count("```") % 2 != 0: return True
    if re.search(r"(def |class |import |return\s*$)", text[-200:]): return True
    if not text.strip().endswith((".", "。", "}", "]", ")", "!", "?", "！", "？")): return True
    return False
```

#### 跨域

在 FastAPI 中配置 CORS（跨源资源共享）。如果前端网页是通过特定端口访问的（比如 http://192.168.0.100:8080），那么这个端口号也必须包含。
 ```
from fastapi.middleware.cors import CORSMiddleware

# ALLOWED_ORIGINS = "http://127.0.0.1, http://192.168.0.100:8080"
origins = os.getenv("ALLOWED_ORIGINS", "http://127.0.0.1").split(",")

# 或者使用列表推导式自动去掉每个地址前后的空格
raw_origins = os.getenv("ALLOWED_ORIGINS", "http://127.0.0.1")
origins = [o.strip() for o in raw_origins.split(",")]
app.add_middleware(
    CORSMiddleware,
    allow_origins     = origins,
    allow_credentials = True,
    allow_methods     = ["*"],
    allow_headers     = ["*"],
)
```

注意：allow_origins 的地址必须包含协议（http/https），且不能有结尾的斜杠 /。
