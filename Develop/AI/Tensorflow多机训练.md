---
source_title: Tensorflow多机训练
categories:
- AI
- Develop
last_modified: '2023-04-01T15:59:29Z'
---
多机训练的方法和单机多卡类似，将 MirroredStrategy 更换为适合多机训练的 MultiWorkerMirroredStrategy 即可。不过，由于涉及到多台计算机之间的通讯，还需要进行一些额外的设置。具体而言，需要设置环境变量 TF_CONFIG ，示例如下:
```
 os.environ['TF_CONFIG'] = json.dumps({
     'cluster': {
         'worker': ["localhost:20000", "localhost:20001"]
     },
     'task': {'type': 'worker', 'index': 0}
 })
```

TF_CONFIG 由 cluster 和 task 两部分组成：
- cluster 说明了整个多机集群的结构和每台机器的网络地址（IP+端口号）。对于每一台机器，cluster 的值都是相同的；
- task 说明了当前机器的角色。例如， {'type': 'worker', 'index': 0} 说明当前机器是 cluster 中的第0个worker（即 localhost:20000 ）。每一台机器的 task 值都需要针对当前主机进行分别的设置。

以上内容设置完成后，在所有的机器上逐个运行训练代码即可。先运行的代码在尚未与其他主机连接时会进入监听状态，待整个集群的连接建立完毕后，所有的机器即会同时开始训练。

提示

请在各台机器上均注意防火墙的设置，尤其是需要开放与其他主机通信的端口。如上例的0号worker需要开放20000端口，1号worker需要开放20001端口。

以下示例的训练任务与前节相同，只不过迁移到了多机训练环境。假设我们有两台机器，即首先在两台机器上均部署下面的程序，唯一的区别是 task 部分，第一台机器设置为 {'type': 'worker', 'index': 0} ，第二台机器设置为 {'type': 'worker', 'index': 1} 。接下来，在两台机器上依次运行程序，待通讯成功后，即会自动开始训练流程。
```
 import tensorflow as tf
 import tensorflow_datasets as tfds
 import os
 import json
```
  
```
 num_epochs = 5
 batch_size_per_replica = 64
 learning_rate = 0.001
```
  
```
 num_workers = 2
 os.environ['TF_CONFIG'] = json.dumps({
     'cluster': {
         'worker': ["localhost:20000", "localhost:20001"]
     },
     'task': {'type': 'worker', 'index': 0}
 })
 strategy = tf.distribute.experimental.MultiWorkerMirroredStrategy()
 batch_size = batch_size_per_replica * num_workers
```
  
```
 def resize(image, label):
     image = tf.image.resize(image, [224, 224]) / 255.0
     return image, label
```
  
```
 dataset = tfds.load("cats_vs_dogs", split=tfds.Split.TRAIN, as_supervised=True)
 dataset = dataset.map(resize).shuffle(1024).batch(batch_size)
```
  
```
 with strategy.scope():
     model = tf.keras.applications.MobileNetV2()
     model.compile(
         optimizer=tf.keras.optimizers.Adam(learning_rate=learning_rate),
         loss=tf.keras.losses.sparse_categorical_crossentropy,
         metrics=[tf.keras.metrics.sparse_categorical_accuracy]
     )
```
  
```
 model.fit(dataset, epochs=num_epochs)
```

在以下测试中，我们在Google Cloud Platform分别建立两台具有单张NVIDIA Tesla K80的虚拟机实例（具体建立方式参见 后文介绍 ），并分别测试在使用一个GPU时的训练时长和使用两台虚拟机实例进行分布式训练的训练时长。所有测试的epoch数均为5。使用单机单卡时，Batch Size设置为64。使用双机单卡时，测试总Batch Size为64（分发到单台机器的Batch Size为32）和总Batch Size为128（分发到单台机器的Batch Size为64）两种情况。

| 数据集 | 单机单卡（Batch Size为64） | 双机单卡（总Batch Size为64） | 双机单卡（总Batch Size为128） |
|:---|:---|:---|:---|
| cats_vs_dogs | 1622s | 858s | 755s |
| tf_flowers | 301s | 152s | 144s |
