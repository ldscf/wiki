---
source_title: Tensorflow 2.3 安装
categories:
- AI
- Develop
last_modified: '2023-04-01T14:42:08Z'
---
TensorFlow™是一个基于数据流编程(dataflow programming)的符号数学系统，被广泛应用于各类机器学习(machine learning)算法的编程实现，其前身是谷歌的神经网络算法库 DistBelief 。

Tensorflow 拥有多层级结构，可部署于各类服务器、PC终端和网页并支持 GPU 和 TPU 高性能数值计算，被广泛应用于谷歌内部的产品开发和各领域的科学研究。

TensorFlow 由谷歌人工智能团队谷歌大脑(Google Brain)开发和维护，拥有包括 TensorFlow Hub、TensorFlow Lite、TensorFlow Research Cloud 在内的多个项目以及各类应用程序接口（Application Programming Interface, API）。自 2015 年 11 月 9 日起，TensorFlow 依据阿帕奇授权协议(Apache 2.0 open source license)开放源代码 。

### 语言与系统支持

TensorFlow 支持多种客户端语言下的安装和运行。截至版本1.12.0，绑定完成并支持版本兼容运行的语言为 C 和 Python，其它（试验性）绑定完成的语言为 JavaScript、C++、Java、Go 和 Swift，依然处于开发阶段的包括 C#、Haskell、Julia、Ruby、Rust 和Scala。

#### Python

TensorFlow 提供 Python 语言下的四个不同版本：
- CPU版本(tensorflow)
- 包含GPU加速的版本(tensorflow-gpu)
- 以及它们的每日编译版本(tf-nightly、tf-nightly-gpu)

TensorFlow 的 Python 版本支持 Ubuntu 16.04、Windows 7、macOS 10.12.6 Sierra、Raspbian 9.0 及对应的更高版本，其中 macOS 版不包含 GPU 加速。
- 安装 Python 版 TensorFlow 可以使用模块管理工具 pip 或 anaconda 并在终端直接运行。
- TensorFlow 在 2.0 之前的 GPU 版本和 CPU 版本是分开的，以后的版本不用区分。
- TensorFlow 从 2.11.0 版本开始，在  windows 上不再支持GPU。

### 依赖
1. Nvidia GPU Driver: CUDA 10.1, 418.x
1. ANACONDA 3-2021.11
1. CUDA 10.1(CupTI)
1. cuDNN SDK 7.6
1. 需要安装 Visual Studio, CUDA 需要 VS 的支持，只需要安装 VS 的核心部件

P.S. Tensorflow 2.3 需要算力不低于 3.5.

### 基础

#### anaconda

Anaconda(大蟒蛇)，是一个开源的 Python 发行版本，可以用于在同一个机器上安装不同版本的软件包及其依赖。包含了 conda、Python 等 180 多个科学包及其依赖项，如：numpy、pandas等。

#### CUDA

CUDA^TM^(Compute Unified Device Architecture)，是显卡厂商 NVIDIA 推出的运算平台。 一种通用并行计算架构，该架构使 GPU 能够解决复杂的计算问题。

包含 CUDA 指令集架构(ISA)以及 GPU 内部的并行计算引擎。

#### cuDNN

NVIDIA cuDNN 是用于深度神经网络的GPU加速库。它强调性能、易用性和低内存开销。NVIDIA cuDNN可以集成到更高级别的机器学习框架中，如谷歌的Tensorflow、加州大学伯克利分校的流行 caffe 软件。简单的插入式设计可以让开发人员专注于设计和实现神经网络模型，而不是简单调整性能，同时还可以在GPU上实现高性能现代并行计算。
- CUDA 与 CUDNN 的关系

cuDNN 是 CUDA 的扩展计算库，支持 CUDA 在 GPU上的深度学习计算。从官方安装指南可以看出，只要把 cuDNN 文件复制到 CUDA 的对应文件夹里就可以，即所谓插入式设计。

### 安装

windows 10-x86_64

#### 一、安装 anaconda 3-2021.11

https://repo.anaconda.com/archive/
- [anaconda3-2021.11-Windows-x86_64](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.11-Windows-x86_64.exe)
- Advanced Options 中，默认是自行添加 anaconda Path，需要手动在 PATH 增加环境变量：我的电脑->属性->高级系统设置->环境变量
```
 C:\ProgramData\Anaconda3\Scripts
```

安装后，会出现 
- Anaconda Navigator (Anaconda3)
- Anaconda Prompt

##### 切换国内源

Anaconda Prompt
```
 conda config --show
```
 
```
 channels:
   - defaults
```
- 恢复默认源
```
 conda config --remove-key channels
```
- 清华源
```
 channels:
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
 ssl_verify: true
```

P.S. Windows 用户无法直接创建名为 .condarc 的文件，可先执行 conda config --set show_channel_urls yes 生成该文件之后再修改。

参考：[Windows 10 如何安装 anaconda](https://liguang.wang/index.php/archives/37/)

#### 二、安装 cudatoolkit 10.1

https://developer.nvidia.com/cuda-10.1-download-archive-update2
- [cudatoolkit 10.1-win10](https://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda_10.1.243_426.00_win10.exe)

P.S. 自定义安装，如果新版本比当前版本新，就安装，否则就把对勾给去掉，保留当前版本。(如果新旧版本一致，即使选了也不安装)
- toolkit 默认安装在 Program Files(C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1)
- Samples 默认安装在 ProgramData(C:\ProgramData\NVIDIA Corporation\CUDA Samples\v10.1)
- 支持 Visual Studio 2015/2017/2019(应该是装一些插件之类的), 需提前安装 VS

#### 三、安装 cudnn 7.6.5

https://developer.nvidia.com/rdp/cudnn-archive

Download cuDNN v7.6.5 (November 5th, 2019), for CUDA 10.1

将下载下来的包解压，然后将下表中的文件放到 NVIDIA GPU Computing Toolkit\CUDA\v10.1 相应目录下。
- [cudnn-10.1-windows10-x64-v7.6.5.32](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/7.6.5.32/Production/10.1_20191031/cudnn-10.1-windows10-x64-v7.6.5.32.zip)

#### 四、安装 tensorflow 2.3
- 创建虚拟环境
```
 conda create -n tensorflow2 python=3.7
```
 
```
 删除虚拟环境
 conda remove -n tensorflow2 --all
```
- 激活虚拟环境
```
 conda activate tensorflow2
```
- 查看虚拟环境

conda info --envs
- 安装 tensorflow 2.3
```
 pip install tensorflow==2.3
```

P.S. 如果上面安装较慢，可以切换 opentuna 的 pip 镜像源(.condarc 在此无用)。
```
 pip config set global.index-url https://opentuna.cn/pypi/web/simple
```
- 验证安装
```
 python
 import tensorflow as tf
 tf.config.list_physical_devices('GPU')
```
