---
source_title: Rust 安装
categories:
- Develop
- Rust
last_modified: '2025-12-18T05:23:06Z'
---
### 安装

一般可以采用通用安装方式：
```
 # Linux/macOS/Windows (Git Bash/WSL)
 # If Windows Subsystem for Linux user run the following in terminal
 curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

验证：
```
 rustc --version
 cargo --version
 rustup --version
```

#### Ubuntu
```
 apt install rustc cargo
```

#### Windows
```
 访问 https://rustup.rs/ 下载 rustup-init.exe
```

Windows 特定依赖
 ```

# 安装 C++ 构建工具
https://visualstudio.microsoft.com/visual-cpp-build-tools/
工作负载 → 勾选：
☑ C++ 生成工具
 
右侧可选组件确保包含：
☑ MSVC v143 - VS 2022 C++ x64/x86 生成工具
☑ Windows 11 SDK (或 Windows 10 SDK)
☑ C++ CMake 工具
```

### Cargo 命令
 ```
cargo new project_name    # 创建新项目
cargo build               # 编译项目
cargo run                 # 编译并运行
cargo check               # 检查代码但不编译
cargo test                # 运行测试
cargo doc                 # 生成文档
cargo fmt                 # 格式化代码
cargo clippy              # 代码 lint 检查
cargo clean               # 清理编译文件
```

#### cargo build
```
 cargo build --release    # 生成正式文件
 cargo run 或 cargo build（默认生成 Debug 版本），以获得最快的编译速度。
 cargo run -p example     # 执行 example 目录下的 main.rs。需要：一、有个相应的目录，里面的 Cargo.toml 中 name = "example"。二、根目录的  Cargo.toml 中 workspace 下有：members = ["example", "other"]
```

#### cargo fix

cargo fix 会自动修改源代码，所以工作目录有未提交的更改（Git），fix 命令出于安全考虑拒绝执行。
 ```
git add .
git commit -m "chore: pre-fix changes"
cargo fix --lib -p demo
cargo test                # 验证修复没有破坏功能
git add .
git commit -m "chore: apply cargo fix"

# 对于开发环境，使用 cargo fix --lib -p demo --allow-dirty 是最快捷的解决方案（不用提交）。对于生产代码，建议先提交更改。
```

#### cargo metadata
```
 cargo metadata --no-deps      # 仅直接依赖的元数据
```

#### .gitignore

一般 Rust 项目，Git 需要忽略一些特定目录、文件，配置参考如下：
 ```

# 编译输出
/target/
**/*.rs.bk

# 临时文件
/tmp/
*.tmp
*.log

# 编辑器文件
.vscode/
.idea/
*.swp
*.swo

# 系统文件
.DS_Store
Thumbs.db
```

#### Cargo.lock

Cargo.toml 里一般只写依赖的版本范围：
```
 serde = "1.0"   # 允许任何 1.0.x 版本
```

Cargo.lock 记录项目依赖的精确版本及依赖树。不仅记录直接依赖，还记录间接依赖（子依赖）。Rust 生态非常依赖递归依赖，Cargo.lock 能保证依赖树一致，防止“版本漂移”。
- Cargo.lock 会记录确切的版本号，保证所有开发者、CI 环境、生产环境使用完全相同的依赖版本。
- 如果没有 Cargo.lock，不同机器上 cargo build 可能会拉取不同的次版本（patch）或子依赖版本，导致行为不一致。

**使用**
1. 二进制项目（可执行程序）：通常 提交 Cargo.lock 到版本控制。  

目的：保证每次构建结果一致，避免版本漂移。
1. 库（library crate）：通常不提交 Cargo.lock。  

因为库会被其他项目引用，锁定依赖会限制使用者升级依赖版本。

### 工具链

Rust 工具链是一套集成的命令行工具集合，是 Rust 开发和项目管理的基石。包括安装、编译、管理依赖、格式化代码、运行测试等功能，是 Rust 开发者的必备工具。

管理命令：rustup
```
 # 查看
 rustup toolchain list
 OR rustup show
```
 
```
 # 设置默认
 rustup default stable-msvc
 OR rustup default 1.69.0
```

### VSCode

扩展
1. rust-analyzer  

必装。提供代码补全、类型提示、错误检查等功能
1. lingma  

通义灵码是由阿里云提供的智能编码辅助工具
1. CodeLLDB  

提供调试支持
1. Even Better TOML  

提供 Cargo.toml 语法高亮
1. Error Lens  

内联错误显示
1. crates  

帮助管理 Cargo.toml 中的依赖
