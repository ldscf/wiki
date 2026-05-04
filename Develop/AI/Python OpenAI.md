---
source_title: Python OpenAI
categories:
- AI
- Develop
- Python
last_modified: '2025-02-26T09:08:03Z'
---
OpenAI Python 库提供了方便的方式来访问 OpenAI 的 REST API。它支持 Python 3.7 及以上版本，并提供同步和异步客户端。

OpenRouter 使用方式与 OpenAI 兼容。

### Inst
```
 pip install openai
```

### 同步方式
 ```
from openai import OpenAI
client = OpenAI(
  base_url="https://openrouter.ai/api/v1",
  api_key="sk-or-v1-b5ecc1909...",
)
model = "deepseek/deepseek-r1-distill-llama-70b:free"
Q="Tell a joke."
completion = client.chat.completions.create(
  model      = model,
  messages   = [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": Q
        }
      ]
    }
  ]
)
print(completion.choices[0].message.content)
print(completion.usage)
```

图片
 ```
"content": [
        {
          "type": "text",
          "text": "What is in this image?"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "https://upload.wikimedia.org/...n-the-nature-boardwalk.jpg"
          }
        }
      ]
```

### 异步方式
