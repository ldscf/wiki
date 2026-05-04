---
source_title: LLaMA
categories:
- AI
- Develop
last_modified: '2025-02-12T02:34:37Z'
---
Llama 是 meta 开源的一款大模型，开源不到一个月的时间就有 19.7K 的 star。2024 年 4 月 19 日，Meta 在官网上官宣了 Llama-3，作为继 Llama-1、Llama-2 和 Code-Llama 之后的第三代模型，Llama-3 在多个基准测试中实现了全面领先，性能优于业界同类最先进的模型。
* **Llama-1:** 2023 年 2 月发布。有 7B、13B、30B 和 65B 四个参数量版本。Llama-1 各个参数量版本都在超过 1T token 的语料上进行了预训训练，模型上下文长度 2,048。其中，最大的 65B 参数的模型在 2,048 张 A100 80G GPU 上训练了近 21天，并在大多数基准测试中超越了具有 175B 参数的 GPT-3。因为开源协议问题，Llama-1 不可免费商用

***Llama-2:** 2023 年 7 月发布了免费可商用版本，有 7B、13B、34B 和 70B 四个参数量版本，除了 34B 模型外，其他均已开源。Llama-2 将预训练的语料扩充到了 2T token，模型上下文长度 4,096，词表大小为 32K。并引入了分组查询注意力机制（grouped-query attention, GQA）等技术。通过进一步的有监督微调（Supervised Fine-Tuning, SFT）、基于人类反馈的强化学习（Reinforcement Learning with Human Feedback, RLHF）等技术对模型进行迭代优化，发布了面向对话应用的微调系列模型 Llama-2 Chat。通过“预训练-有监督微调-基于人类反馈的强化学习”这一训练流程，Llama-2 Chat 不仅在众多基准测试中取得了更好的模型性能，同时在应用中也更加安全。Meta 在 2023 年 8 月发布了专注于代码生成的 Code-Llama，共有 7B、13B、34B 和 70B 四个参数量版本

***Llama-3:** 2024 年 4 月，Meta 正式发布了开源大模型 Llama 3，包括 8B 和 70B 两个参数量版本。400B 参数量的版本计划在 2024 年 7 月 23 日发布。Llama-3 支持 8K 长文本，并采用了一个编码效率更高的 tokenizer(sentencepiece -> tiktoken, GPT4 使用 tiktoken)，词表大小为 128K。在预训练数据方面，Llama-3 使用了超过 15T token 的语料

***Llama 3.1:** 2024 年 7 月 24 日，Meta 正式发布新一代开源大模型 Llama 3.1 系列，提供 8B、70B 及 405B 参数版本。使用 1.6 万个 H100 GPU、以及超过 15T token 的公开数据进行训练。 架构方面，该模型选择标准的解码器 transformer 模型架构进行调整，而不是混合专家模型，以最大化训练稳定性。采用了迭代的后训练程序，每一轮使用监督微调和直接偏好优化。其上下文长度被提升至 128K，而模型参数也被提高到了 4050 亿规模，是近年来规模最大的大语言模型之一。该模型在通用常识、可引导性、数学、工具使用和多语言翻译等广泛任务中足以对标 GPT-4、Claude 3.5 Sonnet 等领先的闭源模型。

![Llama3.1-405B.jpg](https://mwbbs.eu.org/wiki/images/1/1c/Llama3.1-405B.jpg)

## 参考
1. [梳理Llama开源家族：从Llama-1到Llama-3](https://cloud.tencent.com/developer/article/2411892)
1. [Llama3-8B到底能不能打？实测对比](https://www.cnblogs.com/bossma/p/18151375)
