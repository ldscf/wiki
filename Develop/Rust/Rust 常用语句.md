---
source_title: Rust 常用语句
categories:
- Develop
- Rust
last_modified: '2025-12-19T03:30:23Z'
---
### 常量
 ```

# const CONSTANT_NAME: Type = value;
const TIMEOUT_SECONDS: u64 = 30;
const DEBUG_MODE: bool = true;
const DEFAULT_LANGUAGE: char = 'en';
```

#### 数学常数

可以直接使用标准库提供的常量。否则 cargo clippy 会报错：
 ```
error: approximate value of `f{32, 64}::consts::PI` found
15 |     let pi: f32 = 3.14;
```
 ```
use std::f64::consts::{PI, E, SQRT_2, FRAC_1_SQRT_2};     //π,e,√2,√2倒数
// std::f32::consts & std::f64::consts
PI                                                π
TAU                                               2π (τ)
E                                                 e
FRAC_1_PI                                         1/π
FRAC_2_PI                                         2/π
FRAC_2_SQRT_PI                                    2/√π
FRAC_PI_2                                         π/2
FRAC_PI_3                                         π/3
FRAC_PI_4                                         π/4
FRAC_PI_6                                         π/6
FRAC_PI_8                                         π/8
LN_2                                              ln(2)
LN_10                                             ln(10)
LOG2_E                                            log₂(e)
LOG10_E                                           log₂(e)
SQRT_2                                            √2
FRAC_1_SQRT_2                                     1/√2
```

### 赋值

#### 基本变量
 ```
let x = 10;                                       // 整数推断为 i32
let y: i64 = 20;                                  // 显式指定类型
let name = "Adam";                                // 字符串切片
let is_active = true;                             // 布尔值
println!("x: {}, y: {}, name: {}, is_active: {}", x, y, name, is_active);
```

#### 向量 (Vector)
 ```
let vec1: Vec = vec![1.1, 2.2, 3.3];         // 显式指定类型
let vec2 = vec![0; 5];                            // [0, 0, 0, 0, 0]
```

#### 数组 (Array)
 ```
let arr1 = [1, 2, 3, 4, 5];                       // 推断为 [i32; 5]
let arr2 = [0u8; 10];                             // 10个零
```

#### 元组 (Tuple)
 ```
let tuple1 = (1, "hello", 3.14);
let tuple2: (i32, bool, f64) = (42, true, 2.718);
```

#### 结构体 (Struct)
 ```
#[derive(Debug)]
struct Person {
    name: String,
    age: u32,
    active: bool,
}
fn main() {
    // 结构体实例化
    let person1 = Person {
        name: String::from("Alice"),
        age: 30,
        active: true,
    };
    
    // 更新语法
    let person2 = Person {
        name: String::from("Bob"),
        age: 25,
        ..person1  // 使用其他实例的剩余字段****
    };
    
    println!("person1: {:?}", person1);
    println!("person2: {:?}", person2);
}
```

#### 枚举 (Enum)
 ```
// 定义枚举
#[derive(Debug)]
enum IpAddr {
    V4(String),
    V6(String),
}
#[derive(Debug)]
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
}
fn main() {
    // 枚举赋值
    let localhost_v4 = IpAddr::V4(String::from("127.0.0.1"));
    let localhost_v6 = IpAddr::V6(String::from("::1"));
    
    let msg_quit = Message::Quit;
    let msg_move = Message::Move { x: 10, y: 20 };
    let msg_write = Message::Write(String::from("hello"));
    
    println!("IPv4: {:?}", localhost_v4);
    println!("消息: {:?}", msg_move);
}
```

#### 哈希映射 (HashMap)
 ```
use std::collections::HashMap;
fn main() {
    // 创建 HashMap
    let mut scores = HashMap::new();
    scores.insert("Blue", 10);
    scores.insert("Red", 20);
    
    // 从向量创建
    let teams = vec!["Blue", "Red"];
    let initial_scores = vec![10, 50];
    let scores2: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();
    
    println!("scores: {:?}", scores);
    println!("scores2: {:?}", scores2);
}
```

### 链式调用

链式调用的核心：每个方法都返回一个可以继续调用方法的对象。一般是最后一个方法消费对象，如 init。

必要条件：
- 同类型链式：返回 Self 或 &mut Self（Builder 模式）
- 变换链式：返回新类型，但继续支持链式（迭代器模式）
- 混合链式：类型在链中变化（管道模式）
 ```
// 1. 配置方法都返回 Self
pub fn level(mut self, level: impl ToString) -> Self { /* ... */ }
pub fn with_file(mut self, dir: &str, prefix: &str) -> Self { /* ... */ }
pub fn with_memory(mut self, max_bytes: usize) -> Self { /* ... */ }
pub fn quiet(mut self) -> Self { /* ... */ }
// 2. 最终方法消费 Self
pub fn init(self) -> (Option, Option) {
    // 消费 self，初始化日志系统
}
// 3. 完整链式调用
let (_guard, _mem_writer) = logger::builder()     // 静态方法创建
    .level("debug")                               // 配置1 → 返回 LoggerBuilder
    .with_file("/tmp", "app")                     // 配置2 → 返回 LoggerBuilder  
    .with_memory(1024)                            // 配置3 → 返回 LoggerBuilder
    .quiet()                                      // 配置4 → 返回 LoggerBuilder
    .init();                                      // 消费 LoggerBuilder，返回结果
```

### serde_json
1. 如果只需要在程序中处理数据，使用 to_value
1. 如果需要输出、存储或传输，使用 to_string
1. 如果需要人类友好的格式，使用 to_string_pretty

### 错误处理

#### ?

? 是 Rust 的错误传播运算符（error propagation operator），用于简化错误处理。函数内使用错误传播运算符 ? 必须返回 Result 或 Option。
 ```
let db = SyncDB::new_from_config(&cfg)?;
// 等价于：
let db = match SyncDB::new_from_config(&cfg) {
    Ok(db) => db,                                 // 成功：继续执行
    Err(e) => return Err(e),                      // 失败：立即返回错误
};
```

#### match

优雅降级（match 处理）。
 ```
let rows = match db.query("SELECT * FROM users").await {
    Ok(r) => r,
    Err(e) => {
        logger::warn!("Query failed: {}", e);
        Vec::new()                                // 返回空列表，继续执行
    }
};
```

### 代码段

#### 时间
 ```
let start_time = Instant::now();                  // 开始计时
... ...
let run_time = start_time.elapsed().as_micros();  // 微秒 (µs)
```
