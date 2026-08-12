---
source_title: Rust Demo
categories:
- Develop
- Rust
last_modified: '2025-11-20T00:57:10Z'
---
下面是一个 Rust 示例，采用分层架构设计，将代码按复用性和职责进行清晰分离。
 ```
demo/
  ├── crates/                    # 通用代码库（可作为独立 crate 发布）
  ├── src/                       # 主项目业务代码
  └── data/                      # 数据文件目录
demo/
 │  Cargo.lock                     # 依赖锁文件，确保每次构建使用相同版本
 │  Cargo.toml                     # 项目配置和依赖定义
 ├─crates/                        # **通用代码库（可作为独立 crate 发布）**
 │  ├─db/                        # 数据库相关组件
 │  └─other/                     # 通用工具组件
 │      │  Cargo.toml             # crate 子库配置和依赖（包名称与顶层 Cargo.toml / dependencies 中一致）
 │      └─src/
 │              lib.rs             # crate 子库目标入口，定义公共 API
 │              umath.rs
 │              ustr.rs
 ├─src/                           # 主项目业务代码
 │  │  lib.rs                     # **库目标入口，定义公共 API**
 │  │  main.rs                    # 可执行文件入口
 │  ├─bin/                       # 额外的二进制文件（每个.rs文件都是独立可执行程序）
 │  ├─common/                    # 通用组件和共享代码
 │  │      mod.rs                 # **模块声明文件**
 │  │      payment.rs
 │  └─utils/                     # 专用工具函数
 │          mod.rs                 # 模块声明文件
 │          ex_payment.rs
 │          ex_ptr.rs
 └─data/                          # 数据文件目录
         product.db
```

=== 分层架构 ===

==== crates ====

可复用基础组件层。包括: db/, network/ 等独立库。
* 代码复用: 这些 crate 可独立发布，供多个项目使用
* 解耦设计: 强制定义清晰的API边界，减少模块间耦合
* 独立测试: 每个crate可单独测试和版本管理
* 团队协作: 不同团队可并行开发不同基础组件

==== src ====

主项目业务逻辑层。包括：

# main.rs         - 应用入口

# lib.rs          - 库接口定义

# common/         - 项目内共享代码

# utils/          - 项目专用工具

# bin/            - 额外可执行程序

* 关注点分离: 业务逻辑与基础设施分离
* 模块化开发: 功能按模块组织，便于维护
* 灵活部署: 既可作为库使用，也可作为独立应用运行
* 示例丰富: bin/ 目录提供使用示例和工具

==== data ====

数据资源层
* 资源隔离: 代码与数据分离，便于部署和管理
* 环境友好: 不同环境可使用不同数据文件
* 版本控制: 可独立管理数据文件版本

=== 定义及引用 ===

==== crates ====

以 other 子目录为例。

===== Cargo.toml =====
 ```
[package]
**name = "u_other"**
version = "0.1.0"
edition = "2021"

[lib]
name = "u_other"

# path 不需要指定，默认使用 src/lib.rs
```

===== src/lib.rs =====
 ```
// 声明子模块
pub mod umath;
pub mod ustr;
// 重新导出公共API
pub use umath::*;
pub use ustr::*;
```

===== src/umath.rs =====
 ```
pub fn add(a : i64, b : i64) -> i64 {
    return a + b;
}

pub fn multiply(a: i32, b: i32) -> i32 {
    a * b
}
```

==== src ====

===== Cargo.toml =====
 ```
[package]
name = "demo"
version = "0.1.0"
edition = "2021"

[dependencies]
u_other = { path = "crates/other" }                      **# u_other，需要与 crates/other/Cargo.toml 中的包名一致。**
serde = { version = "1.0", features = ["derive"] }
thiserror = "1.0"
[bin](#bin)
name = "demo"
path = "src/main.rs"
```

##### main.rs
 ```
use demo::*;          // ex_payment, ...
use u_other::add;
fn main() {
    println!("{:?}", add(1, 2));
    // Payment
    ex_payment();
    
}
```

##### lib.rs
 ```
// 声明子模块
pub mod utils;
pub mod common;
// 重新导出主要功能，提供简洁的API
pub use utils::*;
pub use common::*;
```

##### utils

###### ex_payment
 ```
use crate::payment::{PaymentStatus, PaymentMethod, Currency, PaymentRequest, PaymentProcessor};
pub fn ex_payment () {
    println!("=== payment ===\n");
    ... ...
}
```

###### mod.rs
 ```
// 声明 utils 模块下的子模块
pub mod ex_code_reuse;
pub mod ex_payment;
// 重新导出工具函数
pub use ex_code_reuse::ex_code_reuse;
pub use ex_payment::ex_payment;
```

##### common

###### payment
 ```
#[derive(Debug, Clone, PartialEq)]
pub enum PaymentStatus {
    Pending,
    Processing,
    Completed { transaction_id: String },
    Failed { reason: String },
    Refunded { refund_amount: f64 },
}
impl PaymentStatus {
    pub fn is_final(&self) -> bool {
        matches!(self, PaymentStatus::Completed { .. } | PaymentStatus::Refunded { .. })
    }
    ... ..
}
... ...
```

###### mod.rs
 ```
// 声明 common 模块下的子模块
pub mod code_reuse;
pub mod payment;
// 重新导出通用功能
pub use code_reuse::*;
//pub use code_reuse::{load_from_file, save_to_file, User, Product};
pub use payment::*;
```
