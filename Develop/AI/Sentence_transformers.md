---
source_title: Sentence transformers
categories:
- AI
- Develop
- Python
last_modified: '2026-03-03T08:12:43Z'
---
sentence-transformer(SBERT) 框架提供了一种简便的方法来计算句子和段落的向量表示（也称为句子嵌入），是用于访问、使用和训练最先进的文本和图像嵌入模型的首选 Python 模块。它可用于模型计算嵌入或使用交叉编码器模型计算相似性，包括语义搜索、语义文本相似性和释义挖掘。

超过 5,000 个预先训练的 Sentence Transformer 模型可供 Hugging Face 立即使用，其中包括 Massive Text Embeddings Benchmark（MTEB） 排行榜中的许多最先进的模型。此外，使用 Sentence Transformer 可以轻松训练或微调您自己的模型，使您能够为特定用例创建自定义模型。

Sentence Transformers 由 UKPLab 创建，由  Hugging Face 维护。

> pip install -U sentence-transformers

Download: [Sentence Transformers List](https://public.ukp.informatik.tu-darmstadt.de/reimers/sentence-transformers/v0.2/)

We recommend Python 3.8+ and PyTorch 1.11.0+.We provide various pre-trained Sentence Transformers models via our Sentence Transformers Hugging Face organization. Additionally, over 6,000 community Sentence Transformers models have been publicly released on the Hugging Face Hub. All models can be found here:
- Original models: [Sentence Transformers Hugging Face organization](https://huggingface.co/models?library=sentence-transformers&author=sentence-transformers)
- Community models: [All Sentence Transformer models on Hugging Face](https://huggingface.co/models?library=sentence-transformers)

### Embedding Mode

| Vector Model | Parameter | Dimension | Token | Size(G) | Memo |
|:---|:---|:---|:---|:---|:---|
| + |
| BAAI/bge-m3 | 1024 | 8192 | 4.6 | multilingual |
| BAAI/bge-large-zh-v1.5 | 1024 | 512 | 1.3 | Chinese |
| mxbai-embed-large | 1024 | 512 | 0.67 |
| uer/sbert-base-chinese-nli | 768 | 512 | 0.39 | zh |
| distiluse-base-multilingual-cased | 512 | 0.52 | 多语言通用语义嵌入模型 |
| all-MiniLM-L6-v2 | 0.23 | 384 | 256 | 0.086 | 语义近似度计算 |
 ```
model = SentenceTransformer('BAAI/bge-large-zh-v1.5')
modules.json: 100%|██████████████████████████████████████████████████████████████████████████████| 349/349 [00:00<00:00, 3.30MB/s]
config_sentence_transformers.json: 100%|█████████████████████████████████████████████████████████| 124/124 [00:00<00:00, 1.15MB/s]
README.md: 100%|█████████████████████████████████████████████████████████████████████████████| 30.4k/30.4k [00:00<00:00, 1.39MB/s]
sentence_bert_config.json: 100%|████████████████████████████████████████████████████████████████| 52.0/52.0 [00:00<00:00, 508kB/s]
config.json: 100%|███████████████████████████████████████████████████████████████████████████| 1.00k/1.00k [00:00<00:00, 10.5MB/s]
pytorch_model.bin: 100%|█████████████████████████████████████████████████████████████████████| 1.30G/1.30G [01:04<00:00, 20.1MB/s]
tokenizer_config.json: 100%|█████████████████████████████████████████████████████████████████████| 394/394 [00:00<00:00, 2.94MB/s]
vocab.txt: 100%|████████████████████████████████████████████████████████████████████████████████| 110k/110k [00:00<00:00, 591kB/s]
tokenizer.json: 100%|██████████████████████████████████████████████████████████████████████████| 439k/439k [00:00<00:00, 1.40MB/s]
special_tokens_map.json: 100%|███████████████████████████████████████████████████████████████████| 125/125 [00:00<00:00, 1.24MB/s]
1_Pooling/config.json: 100%|██████████████████████████████████████████████████████████████████████| 191/191 [00:00<00:00, 857kB/s]

# code
from sentence_transformers import SentenceTransformer, util
model = SentenceTransformer("...MODEL...")
emb1 = model.encode('猫是一种小型哺乳动物，被视为宠物。')
emb2 = model.encode('宠物猫')
cos_sim = util.pytorch_cos_sim(emb1, emb2)
print(cos_sim)

# model
model = SentenceTransformer('BAAI/bge-large-zh-v1.5')
#>>> tensor([0.6472](#0.6472))
model = SentenceTransformer('uer/sbert-base-chinese-nli')
#>>> tensor([0.6135](#0.6135))
model = SentenceTransformer('distiluse-base-multilingual-cased')
#>>> tensor([0.6646](#0.6646))

# 导出模型

## model.save("/tmp/bge-large-zh-v1.5")

# 导入本地模型

## model = SentenceTransformer('/tmp/bge-large-zh-v1.5')
```

### 同种语义句向量对比
 ```
from sentence_transformers import SentenceTransformer, util
model = SentenceTransformer('distiluse-base-multilingual-cased')
emb1 = model.encode('Natural language processing is a hard task for human')
emb2 = model.encode('自然语言处理对于人类来说是个困难的任务')
emb3 = model.encode('猫是一种小型哺乳动物，被视为宠物。')
cos_sim = util.pytorch_cos_sim(emb1, emb2)
print(cos_sim)
cos_sim = util.pytorch_cos_sim(emb1, emb3)
print(cos_sim)
```
```
 # result
 tensor([0.8960](#0.8960))
 tensor([0.1019](#0.1019))
```

### 向量搜索

模型 uer/sbert-base-chinese-nli 的效率，在 6PCPU, 12 GRAM 主机上，大约 15 段/s(红楼梦 120 回分 3150 段)
 ```
import pandas as pd
df = pd.read_csv("demo.txt", sep="#",header=None, names=["sentence"])
from sentence_transformers import SentenceTransformer
model = SentenceTransformer('uer/sbert-base-chinese-nli')
sentences = df['sentence'].tolist()
sentence_embeddings = model.encode(sentences)
import faiss
dimension = sentence_embeddings.shape[1]
index = faiss.IndexFlatL2(dimension)
index.add(sentence_embeddings)
topK = 5
search = model.encode(["宠物猫"])
D, I = index.search(search, topK)
df['sentence'].iloc[I[0]]
```
```
 # result
 6     猫是一种小型哺乳动物，被视为宠物。
 3       鸡是一种小型哺乳动物，下蛋用。
 4     桌子是一种家俱，放东西、吃饭使用。
 7    狗是一种常见的宠物，被用作伴侣动物。
 5           椅子是一种家俱，人坐。
 Name: sentence, dtype: object
 >>> 
 >>> D
 array([341.92676, 638.62585, 787.46716, 848.5363 , 861.7328 ](#341.92676,_638.62585,_787.46716,_848.5363_,_861.7328_), dtype=float32)
 >>> I
 array([6, 3, 4, 7, 5](#6,_3,_4,_7,_5))
```
```
 # demo.txt
 大米是一种食品，通常煮饭吃。
 马是一种大型哺乳动物，可以在战场上。
 驴是一种大中型哺乳动物，拉磨使用。
 鸡是一种小型哺乳动物，下蛋用。
 桌子是一种家俱，放东西、吃饭使用。
 椅子是一种家俱，人坐。
 猫是一种小型哺乳动物，被视为宠物。
 狗是一种常见的宠物，被用作伴侣动物
```
```
 # sentence_embeddings
 array([[ 0.11093175,  1.1299301 ,  0.17589748, ...,  0.37643528,
```

           -0.12583402,  0.00688386],

          [ 0.8285252 ,  0.56754893,  0.8541247 , ..., -0.02121782,

            0.24608377, -0.8908539 ],

          [ 0.8924473 ,  0.39356   ,  1.0510321 , ..., -0.28610831,

            0.61363673, -0.98788214],

          ...,

          [ 0.5105152 ,  0.45100805,  0.05316412, ...,  0.8938415 ,

            0.93790394,  0.23845226],

          [-0.35107028, -0.48179546,  0.13401322, ...,  1.0194194 ,

            1.2652978 ,  0.60486746],

          [ 0.68396544,  1.0733061 ,  0.99040854, ..., -0.5946619 ,

            0.9970803 ,  0.17561308]], dtype=float32)
 
```
 # sentence_embeddings.shape
 (8, 768)
```

### 向量搜索(增加向量)
 ```
sentences_add = ["《红楼梦》，中国古代章回体长篇小说，中国古典四大名著之一。其通行本共 120 回，一般认为前 80 回是清代作家曹雪芹所著，后 40 回作者为无名氏，整理者为程伟元、高鹗。小说以贾、史、王、薛四大家族的兴衰为背景，以富贵公子贾宝玉为视角，以贾宝玉与林黛玉、薛宝钗的爱情婚姻悲剧为主线，描绘了一些闺阁佳人的人生百态，展现了真正的人性美和悲剧美，是一部从各个角度展现女性美以及中国古代社会百态的史诗性著作。", "《三国演义》（又名《三国志演义》《三国志通俗演义》）是元末明初小说家罗贯中根据陈寿《三国志》和裴松之注解以及民间三国故事传说经过艺术加工创作而成的长篇章回体历史演义小说，与《西游记》《水浒传》《红楼梦》并称为中国古典四大名著。"]
sentence_add_embeddings = model.encode(sentences_add)
index.add(sentence_add_embeddings)
df = pd.DataFrame([*sentences, *sentences_add], columns=["sentence"])
search = model.encode(["章回体小说"])
D, I = index.search(search, topK)
df['sentence'].iloc[I[0]]
```

### Error
* OSError: We couldn't connect to 'huggingface.co' to load this file
 ```
1. 可以使用镜像网站下载
 export HF_ENDPOINT=https://hf-mirror.com
 再进入 python 即可。
```
