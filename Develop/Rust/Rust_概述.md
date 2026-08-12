---
source_title: Rust 概述
categories:
- Develop
- Rust
last_modified: '2025-10-24T02:00:14Z'
---
Rust 是一门由 Mozilla 主导开发、现在由社区驱动的通用、编译型系统编程语言。它的设计核心宗旨是“安全、并发、高效”，旨在解决长期困扰系统级编程的内存安全和并发编程问题。

### 演进

#### 诞生与早期发展 (2006 - 2010)
- 2006 年： Rust 作为 Graydon Hoare 的个人项目首次出现，目标是创建一门在不牺牲性能的前提下保障内存安全的系统级语言。
- 2009 年： Graydon Hoare 成为 Mozilla 的雇员，Rust 项目得到 Mozilla 的支持。
- 2010 年： Rust 首次作为 Mozilla 官方项目对外公布。最初的编译器是用 OCaml 语言编写的。

#### 编译器自举与 LLVM 后端 (2010 - 2014)
- 2010 - 2011 年： Rust 编译器开始从 OCaml 迁移到用 Rust 语言自身编写，实现了自举（Self-hosting），这个编译器被称为 `rustc`。
- 2011 年： 成功完成自举，并开始采用 LLVM 作为编译器后端，这为 Rust 的高性能和跨平台能力打下了坚实基础。
- 2012 年： 发布第一个有版本号的预览版编译器。
- 2013 年： Mozilla 宣布与三星合作开发 Servo 网页浏览器排版引擎，该引擎由 Rust 实现，成为推动 Rust 语言设计和改进的重要“试验场”。

#### 稳定版和版本（Edition）制度 (2015 至今)

Rust 遵循严格的 6 周发布周期，每 6 周都会发布一个新的稳定版本。
- Rust 2015： Rust 1.0.0 稳定版正式发布。这是一个里程碑事件，标志着 Rust 语言及其核心库的稳定。一旦达到 1.0，项目承诺不会破坏现有稳定版代码的兼容性。
- Rust 2018： 引入了 async/await 异步编程语法、简化了模块系统（use 路径）、并改进了生命周期语法。
- Rust 2021： 引入了新的 trait 求解器、默认 Cargo.toml 中使用 edition = "2021"、以及一些语法上的微调。

### 高性能

Rust 被设计为一种系统级编程语言，其目标是提供接近 C/C++ 的性能，在内存安全上进行了优化。
- 零成本抽象 (Zero-Cost Abstractions)

泛型（Generics）、Trait（类似接口）和闭包等高级语言特性在编译时被优化，运行时几乎不产生额外开销，就像手写了高效的底层代码一样。
- 内存布局控制

允许开发者精确控制数据结构在内存中的布局（例如使用 repr(C)），这对于优化缓存命中和与 C/C++ FFI 交互至关重要。                                            * 更好的优化潜力

严格的别名规则（通过借用系统保证）和不可变性（Immutable by default）让 LLVM 编译器能进行更激进和有效的优化，因为它可以更自信地知道哪些数据在执行过程中不会改变或被其他地方访问。

### 高并发

Rust 在高并发（High Concurrency）方面表现非常出色，是构建高性能、高可靠的并发系统的理想选择之一。
- 所有权和借用系统 (Ownership and Borrowing)

Rust 的编译器通过其所有权和借用规则，在编译阶段就能确保没有数据竞争 (Data Races)。这意味着，在使用多线程时，不会遇到像 C/C++ 中常见的因忘记加锁、双重释放或释放后使用等导致的内存安全和线程安全问题。
- Send 和 Sync Trait

Rust 通过内置的 Send 和 Sync trait 来严格控制数据在线程间的传递和共享，进一步强化了并发安全性。

### 源代码的保护性

Rust 被编译成本地机器码（native code），即源码直接编译成对应操作系统和 CPU 架构的本机机器码，不容易方便地完全恢复成原始的源代码。

### 运行时，对环境的要求
- 对 C 库 (libc) 的依赖: Rust 的标准库 (std) 会动态链接到 C 标准库。（Linux: glibc, Windows: msvcrt, macOS: libSystem）  

如在 glibc 2.31 ( Ubuntu 20.04) 上编译了一个程序，可能无法在一个 glibc 2.19 (CentOS 7) 的系统上运行

在 Linux 上，可以使用 musl target 来编译一个完全静态的二进制文件，不依赖任何外部库（包括 glibc）。这个二进制文件可以在任何 x86\_64 架构的 Linux 内核上运行（只要内核版本兼容）。
 ```
rustc --target=x86_64-unknown-linux-musl main.rs
cargo build --target x86_64-unknown-linux-musl
cargo run --target x86_64-unknown-linux-musl

# .cargo/config.toml
[build]
target = "x86_64-unknown-linux-musl"

# 注意：如果你的代码使用了 cc crate (例如依赖 C 库)，你可能还需要配置

# [target.x86_64-unknown-linux-musl]

# linker = "musl-gcc" 
```

### 跨平台编译

跨平台编译(Cross-Compilation)，如在 Linux 上编译 Windows 可执行文件。
```
 # 环境
 # apt install gcc-mingw-w64-x86-64
 # rustup target add x86_64-pc-windows-gnu
```
 
```
 # 编译
 rustc dp1.rs --target=x86_64-pc-windows-gnu -o dp.exe
```
 
```
 cargo build --target=x86_64-pc-windows-gnu
```
 
```
 # 完全静态编译的二进制文件
 # rustup target add x86_64-unknown-linux-musl
 cargo build --release --target x86_64-unknown-linux-musl   # 编译优化版本
```
