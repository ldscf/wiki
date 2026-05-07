---
source_title: Word2vec
categories:
- AI
- Develop
- Python
last_modified: '2024-05-30T09:17:29Z'
---
Word2Vec 是 Google 在 2013 年推出的一个 NLP 工具，能够将单词转化为向量来表示，可以定量的去度量词与词之间的关系，挖掘词与词之间的联系。
- 连续词袋(Continuous Bag of Words, CBOW)，根据周围的单词预测中心词
- Skip-Gram，根据中心词预测周围的单词

Word2Vec 的应用广泛而多样。最流行的应用之一是文本分类，它用于根据单词的含义对文本进行分类。它还用于情感分析，它可以通过分析所用单词的含义来确定一段文本的情感。

Word2Vec 的另一个重要应用是机器翻译，它可以通过识别单词的正确含义来帮助提高翻译的准确性。它还用于推荐系统，它可以根据用户查询的含义推荐产品或服务。

### INST
```
 pip3 install gensim
 *<small> Collecting gensim
```

   Downloading gensim-4.3.2-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (26.6 MB)

      |████████████████████████████████| 26.6 MB 7.7 MB/s 
```
 Requirement already satisfied: numpy>=1.18.5 in /usr/local/lib/python3.8/dist-packages (from gensim) (1.23.4)
 Collecting scipy>=1.7.0
```

   Downloading scipy-1.10.1-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (34.5 MB)

      |████████████████████████████████| 34.5 MB 8.0 MB/s 
```
 Collecting smart-open>=1.8.1
```

   Downloading smart_open-7.0.4-py3-none-any.whl (61 kB)

      |████████████████████████████████| 61 kB 1.2 MB/s 
```
 Collecting wrapt
```

   Downloading wrapt-1.16.0-cp38-cp38-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl (83 kB)

      |████████████████████████████████| 83 kB 1.8 MB/s 
```
 Installing collected packages: scipy, wrapt, smart-open, gensim
 Successfully installed gensim-4.3.2 scipy-1.10.1 smart-open-7.0.4 wrapt-1.16.0*
```

### demo
```
 import gensim.downloader as api
 from gensim.models import Word2Vec
```
 
```
 *<small>## train a CBOW model from text8
 # dataset = api.load("text8")
 # model_cbow = Word2Vec(sentences=dataset, sg=0, window=5, vector_size=100, min_count=5, workers=4)
 ## save model
 # model_cbow.save('text8_model_cbow.model')
 # retrain
 # model_cbow.train([["hello"],["world"]], total_examples=1, epochs=1)*
```
 
```
 target_words = ["king", "queen", "man", "woman"]
 model_cbow = Word2Vec.load('text8_model_cbow.model')
```
 
```
 *<small>#### wv.save 以 kededVectors 实例形式保存词向量文件，因为无完整的模型状态，无法再训练
 ## 使用 KeyedVectors.load 加载词向量文件
 ## model_cbow.wv.save("text8_model_cbow_base.model")*
```
 
```
 # Get the word vectors for the specified words
 word_vectors = [model_cbow.wv[word] for word in target_words]
```
 
```
 # pip3 install matplotlib scikit-learn
 import matplotlib.pyplot as plt
 from sklearn.decomposition import PCA
```
 
```
 # Reduce dimensionality using PCA for visualization
 pca = PCA(n_components=2)
 word_vectors_pca = pca.fit_transform(word_vectors)
```
 
```
 # Plot word vectors and annotations
 plt.figure(figsize=(10, 8))
 for i, word in enumerate(target_words):
```

     plt.annotate(word, (word_vectors_pca[i, 0], word_vectors_pca[i, 1]), fontsize=10)

     plt.arrow(0, 0, word_vectors_pca[i, 0], word_vectors_pca[i, 1], head_width=0.1, head_length=0.1, fc='blue', ec='blue')
```
 plt.title('Word Vectors and Annotations')
 plt.xlabel('Principal Component 1')
 plt.ylabel('Principal Component 2')
 plt.grid()
 plt.axhline(y=0, color='black', linewidth=0.5)
 plt.axvline(x=0, color='black', linewidth=0.5)
 plt.show()
```
