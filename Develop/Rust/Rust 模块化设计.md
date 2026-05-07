---
source_title: Rust 模块化设计
categories:
- Develop
- Rust
last_modified: '2025-11-03T08:09:47Z'
---
Rust 提供了一个强大的模块系统（module system）来支持分层、封装、重用与隔离。

### 基本概念

| 层次 | 关键字 / 文件 | 说明 |
| Crate | lib.rs / main.rs | 一个独立的编译单元，可以是库或可执行文件 |
| Module | mod xxx | 逻辑代码组织单元，用于控制作用域与可见性 |
| Package | Cargo.toml | 一个或多个 crate 的集合（常见是一个） |
| Workspace | 顶层 Cargo.toml | 多 crate 项目共享依赖与构建设置的集合 |

### 可见性

| 关键字 | 可见性 |
| pub | 对所有模块可见（包括外部 crate） |
| pub(crate) | 对当前 crate 可见 |
| pub(super) | 对父模块可见 |
| 默认（无修饰） | 仅模块内可见 |
```
 *    pub        fn visible_in_public() {}
```

     pub(crate) fn visible_in_crate() {}

     pub(super) fn visible_in_parent() {}

                fn only_here() {}*

### 模块化方式

#### 内联模块

Inline module，所有内容都定义在同一个文件中。
 ```
// main.rs
mod math {
    pub fn add(a: i32, b: i32) -> i32 {
        a + b
    }
    fn secret() {
        println!("private function");
    }
}
fn main() {
    println!("{}", math::add(2, 3)); // OK
    // math::secret(); // ❌ 不可见
}
```

#### 文件模块

File module，将模块内容放入单独文件中。
 ```
// main.rs
mod math;
fn main() {
    println!("{}", math::add(10, 20));
}
// math.rs
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

#### 文件夹模块

Directory module，适合复杂层级结构。
 ```
src/
├── main.rs
└── math/
    ├── mod.rs
    ├── add.rs
    └── utils.rs
// main.rs
mod math;
use math::add::add_two;
fn main() {
    println!("{}", add_two(5));
    println!("{}", math::add::add_two(6));
}
>>--------------------
// math/mod.rs
pub mod add;
mod utils; // 内部模块，供 add 使用
// pub use add::add_two;
>>--------------------
// math/add.rs
use super::utils;
pub fn add_two(x: i32) -> i32 {
    utils::double(x) / 2
}
>>--------------------
// math/utils.rs
pub fn double(x: i32) -> i32 {
    x * 2
}
```

说明：
* main.rs
```
 # mod math
 查找 math.rs 或 math/mod.rs
 # use math::add::add_two;
 math::add::add_two(6) 可以简写为：add_two(6)
```
* 关于 math/add.rs 中的 use super::utils
```
 add 和 utils 是同一个父模块（main）下的兄弟关系模块，兄弟模块之间不能直接访问。必须通过共同的父模块 math 来访问——也就是在 math/mod.rs 中声明的 mod utils。
```

### 最佳实践

#### 逻辑分层
 ```
src/
├── main.rs          // 入口
├── lib.rs           // 核心库
├── config/
│   ├── mod.rs
│   └── loader.rs
├── services/
│   ├── mod.rs
│   ├── user.rs
│   └── order.rs
└── utils/
     ├── mod.rs
     └── logger.rs
```

#### 公有接口

Public API，在每个顶层模块中导出需要对外暴露的接口。
 ```
// services/mod.rs
pub mod user;
pub mod order;
// 对外导出统一接口
pub use user::UserService;
pub use order::OrderService;
```

外部使用
```
 use crate::services::{UserService, OrderService};
```

#### 单元测试

Rust 支持在模块内部定义测试模块。
 ```
1. [cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
    }
}
```
