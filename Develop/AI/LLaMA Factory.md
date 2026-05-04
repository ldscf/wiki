---
source_title: LLaMA Factory
categories:
- AI
- Develop
last_modified: '2025-01-23T08:32:40Z'
---
LLaMA-Factory 是一个开源项目，它提供了一套全面的工具和脚本，用于微调、服务和基准测试 LLaMA 模型。[[Ollama|LLaMA]] 是 Meta AI 开发的一组基础语言模型。

[LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)提供以下内容：
- 数据预处理和标记化的脚本
- 用于微调 LLaMA 模型的训练流程
- 使用经过训练的模型生成文本的推理脚本
- 评估模型性能的基准测试工具
- 用于交互式测试的 Gradio Web UI

### 环境

#### 软件

| 必需项 | 至少 | 推荐 |
|:---|:---|:---|
| python | 3.8 | 3.11 |
| torch | 1.13.1 | 2.4.0 |
| transformers | 4.41.2 | 4.43.4 |
| datasets | 2.16.0 | 2.20.0 |
| accelerate | 0.30.1 | 0.32.0 |
| peft | 0.11.1 | 0.12.0 |
| trl | 0.8.6 | 0.9.6 |
| 可选项 | 至少 | 推荐 |
|:---|:---|:---|
| CUDA | 11.6 | 12.2 |
| deepspeed | 0.10.0 | 0.14.0 |
| bitsandbytes | 0.39.0 | 0.43.1 |
| vllm | 0.4.3 | 0.5.0 |
| flash-attn | 2.3.0 | 2.6.3 |

#### 硬件

| 方法 | 精度 | 7B | 13B | 30B | 70B | 110B | 8x7B | 8x22B |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Full | 32 | 120GB | 240GB | 600GB | 1200GB | 2000GB | 900GB | 2400GB |
| Full | 16 | 60GB | 120GB | 300GB | 600GB | 900GB | 400GB | 1200GB |
| Freeze | 16 | 20GB | 40GB | 80GB | 200GB | 360GB | 160GB | 400GB |
| LoRA/GaLore/APOLLO/BAdam | 16 | 16GB | 32GB | 64GB | 160GB | 240GB | 120GB | 320GB |
| QLoRA | 8 | 10GB | 20GB | 40GB | 80GB | 140GB | 60GB | 160GB |
| QLoRA | 4 | 6GB | 12GB | 24GB | 48GB | 72GB | 30GB | 96GB |
