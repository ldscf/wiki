---
source_title: NVIDIA 托管模型评估
categories:
- AI
- Develop
last_modified: '2026-10-21T05:53:07Z'
---
| # | 模型名称 | 参数量（总/激活） | 上下文窗口 | SWE-bench / 核心评估指标 | 适用场景 | 评星 |
|:---|:---|:---|:---|:---|:---|:---|
| + NVIDIA 托管模型评估（Free Endpoint） |
| 1 | **rfd4-proteina** | 专有生物架构 | N/A | TM-score: 0.88 (蛋白质设计) | 生物医药, 抗体与蛋白质生成设计, 酶工程 | ★★★★★ |
| 2 | **nemotron-3.5-super-vl-120b-a12b** | 120B / 12B (MoE) | 1M (1000K) | Terminal-Bench: 38.2% | Tool-Calling: 92.5% | 长程Agent, 架构图/UI转代码, 自动化Debug, 视觉推理 | ★★★★★ |
| 3 | **deepseek-v4.1-flash** | 552B / 8B (MoE) | 200K - 1M | SWE-bench: 79.0% | PinchBench: 81.7% | IDE代码高频补全, CI/CD自动纠错, 高吞吐Agent | ★★★★★ |
| 4 | **glm-5-3** | 753B (MoE) | 200K | SWE-bench Pro: 58.4% | Terminal-Bench: 63.5% | 硬核系统重构, 深层Bug排查, 算法推导, 架构设计 | ★★★★★ |
| 5 | **nemotron-3-ultra-550b-a55b** | 550B / 55B (MoE) | 1M (1000K) | Tool-Calling: 91.0% | Terminal-Bench: 36.4% | 企业级CI/CD总调度, 超大代码库日志分析, 复杂工具链编排 | ★★★★★ |
| 6 | **nemotron-3.5-rerank-8b** | 8B (Dense) | 128K | MRR@10: 82.4% (RAG重排序) | RAG图文混合检索, 语义重排序, 文档检索 | ★★★★☆ |
| 7 | **nemotron-3.5-embed-8b** | 8B (Dense) | 32K | MTEB: 69.8 (高精度表征) | 企业级RAG, 复杂图文PDF/表格向量化 | ★★★★☆ |
| 8 | **Step 5 Preview** | ~600B / 27B (MoE) | 1M (1000K) | LiveCodeBench: 82.1% | MMMU: 78.4% | 多模态长文本重构, Agent自动化, 跨模态分析 | ★★★★☆ |
| 9 | **Kumo Relational** | 专用图/关系架构 | N/A | GNN-AUC: 0.91 (多表关联预测) | 关系型数据库预测, 用户流失分析, 复杂图数据挖掘 | ★★★★☆ |
| 10 | **glm-5-3-flash** | 320B / 18B (MoE) | 200K | LiveCodeBench: 78.5% | Toolathon: 46.3% | 闪电多模态Agent, 快速代码生成, 自动化UI分析 | ★★★★☆ |
| 11 | **nemotron-3.5-lightning-30b-a3b** | 30B / 3B (MoE) | 128K | HumanEval: 82.1% | TTFT: <50ms | 端侧闪电代码补全, 极速Shell命令生成, 专有Agent | ★★★★☆ |
| 12 | **muse-glimmer-30b** | 30B (Dense) | 128K | ChartQA: 84.2% | MMMU: 71.5% | 流程图/电路图解析, 视觉多模态推理, 结构化提取 | ★★★★☆ |
| 13 | **laguna-xs-2.1** | 33B (MoE) | 256K | Terminal-Bench: 68.5% | SWE-bench Multi: 74.2% | 端侧长程Agent编程, 命令行自动化, 本地代码重构 | ★★★★☆ |
| 14 | **nemotron-3.5-content-safety** | 8B (Dense) | 16K | Moderation F1: 0.94 | 多语言多模态文本/图像内容安全审查, Guardrails | ★★★★☆ |
| 15 | **cosmos3-nano** | 专有物理扩散架构 | 视频/控制流 | FVD: 85.2 (物理视频生成) | 具身智能, 物理世界生成, 自动驾驶轨迹与动作预测 | ★★★★☆ |
| 16 | **cosmos3-nano-reasoner** | 专有VLM架构 | 视频/图像帧 | Physical-Reasoning: 81.4% | 视频物理世界时空推理, 动态场景理解, 碰撞预测 | ★★★★☆ |
| 17 | **nemotron-3-nano-omni-30b-a3b-reasoning** | 30B / 3B (Omni-MoE) | 128K | Realtime Latency: <120ms | 实时音视频流原生交互, 听音辨字+看图分析, 现场推理 | ★★★★☆ |
| 18 | **gemma-4-31b-it** | 31B (Dense) | 128K | LiveCodeBench: 81.5% | HumanEval: 89.2% | 强类型语言开发 (Rust/Java), 静态代码审查, 逻辑推理 | ★★★★☆ |
| 19 | **llama-3.1-nemotron-safety-guard-8b-v3** | 8B (Dense) | 128K | Safety F1: 0.95 | 大模型安全护栏, 文本输入/输出合规过滤 | ★★★★☆ |
| 20 | **llama-guard-4-12b** | 12B (Dense) | 128K | Multimodal Safety: 93.8% | 多模态 (图文) 安全分类, 提示词/响应合规审查 | ★★★★☆ |
| 21 | **magpie-tts-zeroshot** | 专有TTS架构 | 短音频样本 | MOS: 4.4 (音质评分) | Zero-shot声音克隆, 情绪化文本转语音 (TTS) | ★★★★☆ |
| 22 | **llama-3.2-11b-vision-instruct** | 11B (Dense VLM) | 128K | DocVQA: 88.2% | ChartQA: 83.1% | 轻量多模态问答, 图像/图表提取, OCR文本理解 | ★★★★☆ |
| 23 | **llama-3.2-90b-vision-instruct** | 90B (Dense VLM) | 128K | DocVQA: 92.5% | MMMU: 69.8% | 深度多模态逻辑推理, 复杂图像/图表分析 | ★★★★☆ |
| 24 | **nemotron-3.5-embed-1b** | 1B (Dense) | 32K | MTEB: 64.2 (向量表征) | 轻量RAG向量化, 文本/图文语义搜索 | ★★★☆☆ |
| 25 | **Kumo Tabular** | 专用预测架构 | N/A | ROC-AUC: 0.89 (表格分类/回归) | 结构化表格预测, 商业风控, 表格In-Context学习 | ★★★☆☆ |
| 26 | **3D Body Pose** | 专用CV架构 | 视频流 | MPJPE: 42.1mm (骨骼姿态估计) | 人体姿态追踪, 3D动作捕捉, 安防与动作分析 | ★★★☆☆ |
| 27 | **riva-translate-4b-instruct-v2** | 4B (Dense) | 16K | BLEU: 42.5 (37语种) | 多语言实时翻译, 小样本Prompt翻译, 跨国文本对齐 | ★★★☆☆ |
| 28 | **ising-calibration-1.5-31b** | 31B (Dense) | 64K | Domain-Acc: 94.2% (量子图表) | 量子计算校准, 实验波形与图表分析, 结构化报告生成 | ★★★☆☆ |
| 29 | **diffusiongemma-26b-a4b-it** | 26B / 4B (Diffusion LLM) | 32K | Parallel TPS: 4.2x (扩散并行) | 实时文本交互, 并行Token生成应用 | ★★★☆☆ |
| 30 | **synthetic-video-detector** | 专有分类微服务 | 视频流 | Detection Acc: 96.8% | AI伪造/合成视频检测, 广播安全, 深度伪造防范 | ★★★☆☆ |
| 31 | **Active Speaker Detection** | 专有CV微服务 | 视频/音频流 | mAP: 88.5% (说话人追踪) | 视频会议说话人定位, 播客编辑, 视频多模态分割 | ★★★☆☆ |
| 32 | **cosmos-transfer2.5-2b** | 2B (扩散架构) | 空间控制流 | Motion Stability: 92% | 自动驾驶合成数据生成, 物理世界模拟, 仿真环境渲染 | ★★★☆☆ |
| 33 | **streampetr** | 专有3D感知架构 | 多帧图像 | NDS: 50.4 (3D目标检测) | 自动驾驶时空3D目标检测, 稀疏查询轨迹传播 | ★★★☆☆ |
| 34 | **gpt-oss-20b** | 20B / 3B (MoE) | 32K | GSM8K: 89.2% | HumanEval: 76.5% | 轻量数学与逻辑推理, 低资源单体代码补全 | ★★★☆☆ |
| 35 | **Background Noise Removal** | 专有音频降噪 | 音频流 | PESQ: 3.85 (降噪评分) | 音频去噪, 语音增强, 实时通信降质修正 | ★★★☆☆ |
| 36 | **sparsedrive** | 专有端到端驾驶 | 传感器数据 | NDS: 54.2 (感知/规划) | 端到端自动驾驶感知、预测与路径规划 | ★★★☆☆ |
| 37 | **bevformer** | 专有Transformer | 多视角图像 | NDS: 52.8 (鸟瞰图感知) | 自动驾驶多视角 Bird's-Eye-View (BEV) 3D感知 | ★★★☆☆ |
| 38 | **Studio Voice** | 专有音频修复 | 音频流 | STOI: 0.96 (清晰度) | 录音室级人声增强, 消除房间混响, 广播音效修复 | ★★★☆☆ |
| 39 | **nemotron-3-embed-1b** | 1B (Dense) | 8K | MTEB: 62.1 (纯文本) | 基础文本语义搜索, 传统向量数据库填充 | ★★☆☆☆ |
| 40 | **ising-calibration-1-35b-a3b** | 35B / 3B (MoE) | 32K | Qubit-Acc: 91.5% | 量子比特校准曲线分析, 物理图表解析 | ★★☆☆☆ |
| 41 | **nemotron-voicechat** | 专有语音 pipeline | 语音流 | WER: <4.2% (英文语音) | 英文实时语音对话助手, 低延迟语音交互 | ★★☆☆☆ |
| 42 | **paligemma** | 3B (Dense VLM) | 8K | RefCOCO: 84.1% | 边缘端/低算力视觉问答, 图像打标, 基础OCR | ★★☆☆☆ |
