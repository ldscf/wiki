---
source_title: C++ 编译
categories:
- C++
- Develop
last_modified: '2025-07-23T02:51:05Z'
---
C++ 程序的编译过程，是将人类可读的源代码转换为机器能够执行的二进制代码的过程。这个过程通常由多个阶段组成，每个阶段负责不同的工作，最终生成可执行文件或库。[编译的四个主要阶段](https://mwbbs.eu.org/wiki/index.php/GCC#编译的四个主要阶段)

### 自定义函数

假定当前目录有 a, b, c 三个 .cpp 文件及相应的 .h 文件。

#### 打包
```
 g++ -c a.cpp -o a.o
 g++ -c b.cpp -o b.o
 g++ -c c.cpp -o c.o
```
 
```
 # Creating static library
 ar rcs abc.a a.o b.o c.o
```
 
```
 # Creating shared library
 g++ -shared -o abc.so a.o b.o c.o
```

#### 调用

假定 main.cpp 中引用了 a.h(目录在：/path/include/)，abc.a/abc.so（目录在：/path/lib/）
```
 # 自定义函数部分使用静态库，运行时不需要 abc.a 及所在目录
 g++ main.cpp -o main -I/path/include -L/path/lib -Wl,-Bstatic -lldur -Wl,-Bdynamic -Wall
```
 
```
 # 使用动态库，运行时，需要 export LD_LIBRARY_PATH=/path/lib/:$LD_LIBRARY_PATH
 g++ main.cpp -o main -I/path/include -L/path/lib -Wall
```

### 链接库

通常为了编译后文件的大小及 BUG 修补等考虑，一般都会采用动态链接库的方式（替换相应的库文件即可）。但有些时候，为了简化使用环境的要求（如避免安装一些不常用也不容易安装的库），编译的时候，也会部分使用静态链接库。

原则：用动态库减少部署体积和便于更新，用静态库解决环境依赖或部署复杂性。

动态链接库：
1. 编译后可执行文件体积小：动态库不打包进最终二进制，节省空间
1. 方便后续 BUG 修复 / 安全补丁：替换 .so 文件即可，主程序无需重编译
1. 多个程序共享内存：系统运行多个使用同一动态库的程序，减少内存占用
1. 编译时间更短：链接器处理速度快，不用复制库代码进程序

部分使用静态库：
1. 简化部署，减少外部依赖：比如 Boost、一些图像/科学计算类第三方库，安装成本高
1. 运行环境无法装库：如运行在只提供标准环境的云函数、老旧系统等
1. 保证版本一致性：静态链接后程序运行不依赖动态库版本，行为稳定
1. 避免稀有库动态链接难题：某些库 .so 安装不便、依赖链复杂（如某些数据库驱动、图形库等）

如：混合链接 Boost（静态）+ 系统库（动态）
 ```
g++ server_demo.cpp -o server_demo -std=c++17 -Wall \
    -I/usr/include/udefcp/include \
    -L/usr/include/udefcp/build/lib \
    -Wl,-Bstatic -lldur -lboost_system -lboost_thread -Wl,-Bdynamic
    -lssl -lcrypto -pthread
```

这样生成的 server_demo 二进制文件：
- 不依赖 Boost 安装（还有自定义的函数 ludr*）
- 只依赖系统标准动态库（libstdc++、pthread 等）及 OpenSSL（-lssl、-lcrypto）

而 OpenSSL 大多时候是要安装的：
```
 apt install libssl-dev 或 dnf install openssl-devel
```
