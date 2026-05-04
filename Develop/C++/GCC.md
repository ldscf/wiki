---
source_title: GCC
categories:
- C++
- Develop
last_modified: '2025-05-23T02:40:00Z'
---
The GNU Compiler Collection (GCC) is a cornerstone in software development, equipping programmers with a robust suite of tools to compile various programming languages. Its significance spans various aspects of development, from enabling the creation of high-performance applications to supporting a multitude of programming paradigms.

Below are key highlights that underscore the utility and versatility of GCC:
- Comprehensive Language Support: In addition to C and C++, GCC extends its capabilities to include Objective-C, Fortran, Ada, Go, and more, facilitating a diverse development ecosystem.
- Advanced Optimization Techniques: GCC implements cutting-edge optimization strategies for developing efficient, high-speed applications.
- Cross-Platform Compatibility: Its support for cross-compiling enables developers to build applications for different platforms and architectures seamlessly.
- Robust Debugging Tools: GCC has extensive debugging features, allowing for detailed examination and troubleshooting of code.
- Open Source Advantage: As an open-source project, GCC benefits from continuous updates and improvements by a global community of developers.
- Extensible Architecture: Its modular design allows adding new languages and features, ensuring that GCC remains at the forefront of technology.
- Compliance with Standards: GCC strives to adhere closely to industry standards for C, C++, and other languages, promoting code portability and compatibility.
- Community and Documentation Support: A strong community and comprehensive documentation assist developers in navigating challenges and leveraging GCC’s full potential.
```
 add-apt-repository ppa:ubuntu-toolchain-r/test
 apt update
 apt install gcc-11 g++-11
```
 
```
 /usr/bin/gcc-11
 /usr/bin/g++-11
```

### 编译的四个主要阶段

| 阶段 | 阶段E | 描述 | 输出文件类型 |
|:---|:---|:---|:---|
| + |
| 1️⃣预处理 | Preprocessing | 处理宏、#include、条件编译等 | .i 文件（纯 C 源代码） |
| 2️⃣ 编译 | Compilation | 将源代码转换为汇编代码 | .s 文件（汇编） |
| 3️⃣ 汇编 | Assembly | 将汇编代码转换为机器指令 | .o 或 .obj 文件（目标文件） |
| 4️⃣ 链接 | Linking | 将多个目标文件合并为可执行文件 | .out / .exe / .elf |

### 编译流程图
```
 *```
```

hello.c
```
  │
  ▼
```

[1] 预处理 (gcc -E)
```
  ↓          └── 宏替换、头文件展开、条件编译
```

hello.i
```
  │
  ▼
```

[2] 编译 (gcc -S)
```
  ↓          └── 语法分析、生成汇编代码
```

hello.s
```
  │
  ▼
```

[3] 汇编 (gcc -c)
```
  ↓          └── 汇编代码 → 机器码（目标代码）
```

hello.o
```
  │
  ▼
```

[4] 链接 (gcc)
```
  ↓          └── 链接库、解析符号，生成可执行程序
```

a.out (可执行文件)

```*

### GCC 编译器命令
```
 *```
```

gcc -E hello.c -o hello.i        # 预处理输出

gcc -S hello.i -o hello.s        # 编译成汇编

gcc -c hello.s -o hello.o        # 汇编成目标文件

gcc hello.o -o hello             # 链接生成最终可执行文件

```*
