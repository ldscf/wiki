---
source_title: Rust 交叉编译
categories:
- Develop
- Rust
last_modified: '2026-09-29T09:10:57Z'
---
本文用于在 Apple Silicon Mac（M1/M2/M3/M4） 上构建以下发布版本：

| 平台 | Target | 产物 |
|:---|:---|:---|
| macOS Universal | aarch64-apple-darwin + x86_64-apple-darwin | Universal |
| Linux x86_64 | x86_64-unknown-linux-musl | ELF |
| Windows x86_64 | x86_64-pc-windows-gnu | .exe |

### 环境

#### macOS
- Apple Silicon Mac
- macOS
- Homebrew
- Rust / Cargo
- rustup
```
 # rustc --version
 # cargo --version
 # rustup --version
```

### 安装交叉编译工具

#### 安装 Zig
```
 brew install zig
```
 
```
 # zig version
 # Linux: 
 pip install ziglang cargo-zigbuild
 echo 'export PATH="$HOME/.local/lib/python3.10/site-packages/ziglang:$PATH"' >> ~/.bashrc
```

#### 安装 cargo-zigbuild
```
 cargo install cargo-zigbuild
```
 
```
 # cargo zigbuild --version
```

### 安装 Rust Target
```
 # rustup target add aarch64-apple-darwin
 rustup target add x86_64-apple-darwin
```
```
 rustup target add x86_64-unknown-linux-musl
 rustup target add aarch64-unknown-linux-musl
```
```
 rustup target add x86_64-pc-windows-gnu
 rustup target add aarch64-pc-windows-gnullvm
```
```
 # rustup target list --installed
```

### 编译 macOS Universal

macOS Universal 需要分别构建 ARM64 和 x86_64，然后使用 lipo 合并。

#### Apple Silicon
```
 cargo build \
```

   --target aarch64-apple-darwin \

   --release

#### Intel
```
 cargo build \
```

   --target x86_64-apple-darwin \

   --release

#### Universal
```
 lipo -create \
```

   target/aarch64-apple-darwin/release/<软件名称> \

   target/x86_64-apple-darwin/release/<软件名称> \

   -output target/dist/<软件名称>

### 编译 Linux x86_64
```
 cargo zigbuild \
```

   --target x86_64-unknown-linux-musl \

   --release

### 编译 Windows x86_64
```
 cargo zigbuild \
```

   --target x86_64-pc-windows-gnu \

   --release
