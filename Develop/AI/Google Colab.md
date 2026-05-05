---
source_title: Google Colab
categories:
- AI
- Develop
last_modified: '2023-07-01T11:08:51Z'
---
什么是 Colab？

Colab 是一种托管式 Jupyter 笔记本服务。借助 Colaboratory（简称 Colab），您可在浏览器中编写和执行 Python 代码，并且：
- 无需任何配置
- 免费使用 GPU
- 轻松共享

无论您是一名**学生**、**数据科学家**还是 **AI 研究员**，Colab 都能够帮助您更轻松地完成工作。
- 在免费版 Colab 中，笔记本最长可以运行 12 小时
- Colab 的资源供应没有保证，也不会无限量供应，用量限额有时会变化
- Colab 中的资源将优先提供给交互式用例
- 我们禁止各种涉及批量计算、会对他人造成负面影响或试图规避我们政策的操作，如：
  - 文件托管、媒体传送或提供其他与 Colab 的交互式计算无关的网络服务
  - 下载种子文件或进行点对点文件共享
  - 使用远程桌面或 SSH
  - 连接到远程代理
  - 加密货币挖矿
  - 运行拒绝服务攻击
  - 破解密码
  - 利用多个帐号绕过访问权限或资源使用情况限制
  - 创建深度伪造内容

### Colab Pro
- £9.72/月 OR $9.99/月，可能分到Tesla V100，即便是Tesla P100，一个月 $9.99 也值
- 每月 100 个计算单元
- Colab Pro 用户可以开 terminal
- 在完成工作后关闭 Colab 标签页，并在没有实际工作需求时避免选用 GPU 或额外的内存
- 可以连接 Google Drive，Google Drive 里可以先存数据集，再解压到 Colab 的 Content 下
- 最长时间是 24 小时断 1 次
- 如果网络不好，可能会频繁断开
- 数据集比较大时候（>10GB），下载到 Colab 的 Content 里可能比较慢，从 Google Drive 解压2.2 GB 的图片文件到 colab 需要不到 30 秒时间
- 分配的RAM可能不足，显示的是 25.5GB，可能实际远低于这个内存

### 地址

https://colab.research.google.com
1. 先点击左上角的 新建笔记本，重命名笔记本
1. 代码执行程序 -> 更改运行时类型 -> 硬件加速器 -> GPU/TPU

### Demo

![Google_Colab.jpg](https://mwbbs.eu.org/wiki/images/2/2d/Google_Colab.jpg)

#### GPU
```
 !nvidia-smi
```
```
 Sat Apr  1 15:15:25 2023       
 +-----------------------------------------------------------------------------+
 | NVIDIA-SMI 525.85.12    Driver Version: 525.85.12    CUDA Version: 12.0     |
 |-------------------------------+----------------------+----------------------+
 | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
 | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
 |                               |                      |               MIG M. |
 |===============================+======================+======================|
 |   0  **Tesla T4**            Off  | 00000000:00:04.0 Off |                    0 |
 | N/A   49C    P8    10W /  70W |      0MiB / 15360MiB |      0%      Default |
 |                               |                      |                  N/A |
 +-------------------------------+----------------------+----------------------+
 +-----------------------------------------------------------------------------+
 | Processes:                                                                  |
 |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
 |        ID   ID                                                   Usage      |
 |=============================================================================|
 |  No running processes found                                                 |
 +-----------------------------------------------------------------------------+
```
```
 import tensorflow as tf
 print("Tensorflow version " + tf.__version__)
 tf.config.experimental.list_physical_devices(device_type=None)
```
```
 Tensorflow version 2.12.0
 [PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'),
  PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]
```

#### TPU
```
 import tensorflow as tf
 print("Tensorflow version " + tf.__version__)
```
 
```
 try:
     tpu = tf.distribute.cluster_resolver.TPUClusterResolver()  # TPU detection
     print('Running on TPU ', tpu.cluster_spec().as_dict()['worker'])
 except ValueError:
     raise BaseException('ERROR: Not connected to a TPU runtime; please see the previous cell in this notebook for instructions!')
```
 
```
 tf.config.experimental_connect_to_cluster(tpu)
 tf.tpu.experimental.initialize_tpu_system(tpu)
 tpu_strategy = tf.distribute.TPUStrategy(tpu)
```
```
 Tensorflow version 2.12.0
 Running on TPU  ['10.46.223.138:8470']
 WARNING:tensorflow:TPU system grpc://10.46.223.138:8470 has already been initialized. Reinitializing the TPU can cause previously created variables on TPU to be lost.
```
