---
source_title: Makefile
categories:
- Develop
- Linux
last_modified: '2025-07-24T01:42:45Z'
---
Makefile 是一个用于自动化构建项目的脚本文件。简单来说，它告诉 make 工具如何编译和链接源代码文件，从而生成可执行程序、库或其他目标文件。当项目包含大量源文件，或者需要复杂的编译参数时，Makefile 非常有用，避免手动输入冗长的编译命令，并确保只有修改过的文件才会被重新编译，大大提高开发效率。

make 工具本身是随着 Unix 系统和 C 语言一起发展起来的，是其生态系统的重要组成部分。虽然 Makefile 也能用于其他语言，但很多语言社区都发展出了更专门、更易用的构建工具，它们通常能更好地与该语言的特性集成。

### 语法结构
 ```
target: dependencies
    command
    command
注意："目标: 依赖"下面的命令行，缩进必须是 。
```
 ```
赋值:
make MODE=DEBUG          # 命令行，优先级最高
override MODE := DEBUG   # Makefile 内强制覆盖赋值
MODE := DEBUG            # Makefile 内赋值
export MODE=DEBUG        # 环境变量赋值
MODE ?= RELEASE          # 条件赋值，当 MODE 未被定义时
优先级: 命令行变量 > override > := / = > export > ?=
“:= 立即赋值” 与 “= 延迟赋值” 的区别:
下面语句能保证 FOO 与 BAR 完全相同
FOO := $(shell date)
BAR := $(FOO)
下面语句在执行到 BAR 时，会对 FOO 执行一次 date，结果几乎不相同
FOO = $(shell date)
BAR = $(FOO)
```
 ```
% 代表一个可以匹配任何非空字符串的占位符，而 %.o: %.cpp（目标: 依赖）会保证左右的 % 取到相同的值
变量替换：
OBJ := $(SRC:.cpp=.o) 表示 将 SRC 变量中所有以 .cpp 结尾的文件名替换为以 .o 结尾，从而生成对应的目标文件（.o 文件）
```
 ```
常用自动化变量:
$@：目标文件（Target），规则左侧定义
$<：第一个依赖文件（First Prerequisite）
$^：所有依赖文件（All Prerequisites）
所以：
%.o: %.cpp
	g++ -c $< -o $@
在执行到某个文件如：funcA.cpp 时，代表:
	g++ -c funcA.cpp -o funcA.o
而:
server_demo: server.o funcA.o funcB.o funcC.o
	g++ -o $@ $^
代表:
	g++ -o server_demo server.o funcA.o funcB.o funcC.o
```
 ```
编译器选项：
-I: 指定包含文件（头文件）的搜索目录
-L: 指定库文件的搜索目录
-Wall: 开启所有常见的编译警告
-std=c++17: 使用 C++17 标准进行编译
-g: 生成调试信息，允许使用调试器（如 GDB）进行调试
-DNDEBUG: 定义 NDEBUG 宏。一般在正式版本中使用，禁用 assert() 宏和一些调试相关的代码，可以减小代码体积并提高性能
-lldur: 链接 libldur.so 或 libldur.a 库
P.S. 通常，当链接一个名为 libldur.a 或 libldur.so 的库时，会使用 -lldur。但当使用 POSIX 线程库（通常是 libpthread.a 或 libpthread.so）是 -pthread，其最基本功能当然是链接线程库，查找并链接 libpthread 库，这部分功能和 -lpthread 类似。但除此外，还会自动设置所有与线程编程相关的、必要的编译器和链接器标志，以确保多线程程序的正确性和性能。
-MMD: 告诉编译器生成一个包含依赖信息的 .d 文件
-MP: 在生成的 .d 文件中为每个头文件添加一个空目标，防止当头文件被删除时 Make 报错
-include: 用于包含其他 Makefile 或依赖文件。前面的 - 表示如果文件不存在，也不报错
P.S. 这三个选项构建了一个自动化的依赖跟踪系统。
include: make 指令，用于将另一个 Makefile 格式文件或文件列表包含到当前 Makefile 中。其前面的 '-' 修饰符，表示这是一个可选的包含，不存在也不报错。
而 -MP 这个选项，很令人迷惑，虽然保证了在 make 过程不报错，但最终还是可能会报错 —— 在编译器无法完成编译的时候，而这才是我们真正需要关注和解决的错误。
-fPIC: 生成位置无关代码 (Position-Independent Code)。这是构建共享库 (.so) 所必需的，因为共享库的代码需要在内存中的任意位置加载和执行
.PHONY: make 指示符，用于将一个或多个目标声明为“伪目标（phony targets）。伪目标代表在 Makefile 中定义的一组动作，而不是指目录中具体的文件。如：.PHONY: all clean dirs $(LIBS)，防止当前目录下碰巧存在一个名为 all、clean、dirs 等文件，不会因为偶然的文件名冲突而行为异常。
如：当前目录下存在一个名为 all 的真实文件，当 make all 时，如果没有定义 .PHONY， make 会先比较目录中 all 这个文件的最后修改时间与 Makefile 中 all 目标所依赖的任何文件。在一般的 Makefile 中是所有的库文件，以及这些库文件依赖的对象文件等的最后修改时间。
优化选项：
-O0: 不进行任何优化，以便调试时代码执行顺序与源代码一致
-O2: 优化级别 2，进行中等程度的编译优化（生产环境中常用的优化），生成更小、运行更快的代码
-O3: 包括一些激进的优化，例如函数内联、循环展开等；运行速度最快，文件可能更大。
-Ofast: 最激进的优化，可能会牺牲浮点运算的精确性，或不严格遵循 IEEE 754 浮点标准)
-Os: 优化代码大小，开启所有不会增加代码大小的 -O2 优化，并额外进行一些旨在缩小代码的优化
```

### 简单例子
 ```

# 如：Makefile
server_demo: server.o funcA.o funcB.o funcC.o
	g++ -o server_demo server.o funcA.o funcB.o funcC.o
server.o: server.cpp
	@echo "Compiling server.cpp ..."
	g++ -c server.cpp
funcA.o: funcA.cpp
	@echo "Compiling funcA.cpp ..."
	g++ -c funcA.cpp
funcB.o: funcB.cpp
	@echo "Compiling funcB.cpp ..."
	g++ -c funcB.cpp
funcC.o: funcC.cpp
	@echo "Compiling funcC.cpp ..."
	g++ -c funcC.cpp
当运行 make 时，会递归地检查各项是否不存在或有最新。
而上面的具体内容，使用变量和模式规则的写法就是：

# Makefile

# 定义最终的可执行文件名称
TARGET := server_demo

# 编译器
CXX := g++

# 定义所有源文件
SRC := server.cpp funcA.cpp funcB.cpp funcC.cpp

# 根据源文件自动生成对应的目标文件（.o 文件）列表
OBJ := $(SRC:.cpp=.o)

# 构建规则

## 默认目标：构建 TARGET
all: $(TARGET)

## 链接规则：将所有 .o 文件链接成可执行文件
$(TARGET): $(OBJ)
	$(CXX) -o $@ $^

## 编译规则：如何将任何 .cpp 文件编译成对应的 .o 文件

## 这是模式规则，适用于所有匹配的 .cpp 到 .o 转换
%.o: %.cpp
	@echo "Compiling $< ..."
	$(CXX) -c $< -o $@

# 清理规则
clean:
	rm -f $(TARGET) $(OBJ)
```

### 部分使用静态库
 ```

# Makefile

# static link: ldur, Boost static, other dynamic link

# ========= Project Configuration =========
TARGET := server_demo
SRC := server_demo.cpp socket.cpp yaml.cpp
OBJ := $(SRC:.cpp=.o)
DEP := $(OBJ:.o=.d)

# ========= Include and Library Paths =========
LDUR_PATH := /usr/include/udefcp
LDUR_INC  := -I$(LDUR_PATH)/include
LDUR_LIB  := -L$(LDUR_PATH)/build/lib

# static link (ldur, Boost static, other dynamic)
STATIC_LINK := -Wl,-Bstatic -lldur -lboost_system -lboost_thread -Wl,-Bdynamic

# ========= Compiler and Linker Settings =========
CXX := g++

# Build mode: debug or release(default)
MODE ?= release
ifeq ($(MODE), release)
	CXXFLAGS := -O2 -DNDEBUG -std=c++17 -Wall $(LDUR_INC)
else
	CXXFLAGS := -g -O0 -std=c++17 -Wall $(LDUR_INC)
endif
LDFLAGS := $(LDUR_LIB) \
           $(STATIC_LINK) \
            -lssl -lcrypto -pthread

# ========= Build Rules =========

# Default target
all: $(TARGET)

# Link all object files into executable
$(TARGET): $(OBJ)
	$(CXX) -o $@ $^ $(LDFLAGS)

# Compile source to object with dependency generation
%.o: %.cpp
	@echo "Compiling $< ..."
	$(CXX) $(CXXFLAGS) -MMD -MP -c $< -o $@

# Include dependency files if they exist
-include $(DEP)

# Clean target
clean:
	rm -f $(TARGET) $(OBJ) $(DEP)

# Info
print:
	@echo "MODE     = $(MODE)"
	@echo "CXX      = $(CXX)"
	@echo "CXXFLAGS = $(CXXFLAGS)"
	@echo "LDFLAGS  = $(LDFLAGS)"
	@echo "SRC      = $(SRC)"
```

### 构建静态库和共享库
 ```
目录结构：
udefcp/
├── Makefile
├── README.md
├── build/
│   ├── lib/
│   │   ├── libldur.a
│   │   └── libldur.so
│   └─── obj/
├── include/
│   ├── ldurbuf.h
│   ├── ldurdebug.h
│   ├── ldurhash.h
│   └── ldurstr.h
├── src/
│   ├── ldurbuf.cpp
│   ├── ldurdebug.cpp
│   ├── ldurhash.cpp
│   ├── ldurhash16.cpp
│   └── ldurstr.cpp
└── test/
```
 ```

# Makefile 

# Directories
SRC_DIR     := src
INCLUDE_DIR := include
BUILD_DIR   := build
OBJ_DIR     := $(BUILD_DIR)/obj
LIB_DIR     := $(BUILD_DIR)/lib

# Libraries and their source files
libldur_SRCS :=	    \
	ldurbuf.cpp    \
	ldurhash.cpp   \
	ldurhash16.cpp \
	ldurdebug.cpp  \
	ldurstr.cpp

# List of all libraries
LIBS := libldur

# Compiler and flags
CXX := g++
CXXFLAGS := -std=c++17 -Wall -fPIC -I$(INCLUDE_DIR)

# Archiver
AR := ar
ARFLAGS := rcs

# All targets
.PHONY: all clean dirs $(LIBS)
all: dirs $(LIBS)
dirs:
	@mkdir -p $(OBJ_DIR)
	@mkdir -p $(LIB_DIR)

# Rule to build object files from source files
$(OBJ_DIR)/%.o: $(SRC_DIR)/%.cpp
	@mkdir -p $(dir $@)
	$(CXX) $(CXXFLAGS) -c $< -o $@

# Rule to build each library (static and shared)
$(LIBS):
	@echo "--- Building library: $@"
	$(eval SRCS := $($(addsuffix _SRCS,$@)))
	$(eval OBJS := $(patsubst %.cpp,$(OBJ_DIR)/%.o,$(SRCS)))
	$(MAKE) $(OBJS)
	@echo "  -> Creating static library: $(LIB_DIR)/$@.a"
	$(AR) $(ARFLAGS) $(LIB_DIR)/$@.a $(OBJS)
	@echo "  -> Creating shared library: $(LIB_DIR)/$@.so"
	$(CXX) $(CXXFLAGS) -shared -o $(LIB_DIR)/$@.so $(OBJS)
clean:
	@echo "--- Cleaning build directory ---"
	@rm -rf $(BUILD_DIR)
```
