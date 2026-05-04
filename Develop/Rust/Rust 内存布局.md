---
source_title: Rust 内存布局
categories:
- Develop
- Rust
last_modified: '2025-11-14T03:12:02Z'
---
Rust 中的内存布局，包括栈内存、堆内存、静态内存等。

### 内存布局

#### 栈内存 (Stack)
- 自动分配和释放：函数调用时分配，返回时自动释放
- 速度快：只是移动栈指针
- 大小固定：编译时已知大小
- LIFO：后进先出结构

#### 堆内存 (Heap)
- 手动管理：需要显式分配（在Rust中通过智能指针）
- 速度较慢：需要寻找合适的内存块
- 动态大小：运行时决定大小
- 灵活：生命周期不受函数调用限制

#### 静态内存 (Static Memory)
- 程序整个生命周期：从启动到结束都存在
- 编译时确定：大小和内容在编译时已知
- 只读或可变：但有严格的访问规则

#### example
```
 *fn mem_example() {
     println!("=== 内存分配示例 ===");
```

 
```
     // 1. 栈内存 - 基本类型
     let stack_integer: i32 = 12;
     let stack_float: f64 = 1.23;
     let stack_bool: bool = true;
```

 
```
     // 2. 栈内存 - 固定大小数组
     let stack_array: [i32; 3] = [1, 2, 3];
```

 
```
     // 3. 堆内存 - 动态数据结构
     let heap_string = String::from("Dynamic String");
     let heap_vector = vec![1, 2, 3, 4, 5];
     let heap_box = Box::new(100);
```

 
```
     // 4. 静态内存 - 字面量和常量
     let static_reference: &str = "I'm static!";
     const PI: f64 = 1.2345;
```

 
```
     // 显示内存地址（近似）
     println!("栈整数: {}", stack_integer);
     println!("堆字符串: {}", heap_string);
     println!("静态引用: {}", static_reference);
     println!("常量PI: {}", PI);
```

 
```
     // 所有权转移 - 堆数据移动
     let moved_string = heap_string;      // 所有权转移，不是数据拷贝
     // println!("{}", heap_string);      // 错误！所有权已移动
```

 
```
     // 栈数据拷贝
     let copied_integer = stack_integer;  // 拷贝值
     println!("原栈整数: {}, 拷贝: {}", stack_integer, copied_integer);
 }*
```

### 内存可视化
 ```
程序内存布局：
 ┌─────────┐
 │   静态内存       │ ← "hello"常量, 静态变量
 │   (程序二进制)   │
 ├─────────┤
 │     栈           │ ← 局部变量, 函数参数
 │    (向下增长)    │
 ├─────────┤
 │     堆           │ ← Box, String, Vec数据
 │    (向上增长)    │
 └─────────┘
```

### 所有权与内存
```
 *fn ownership_and_memory() {
     // 栈数据 - Copy trait
     let a = 10;
     let b = a;                             // 拷贝值
     println!("a: {}, b: {}", a, b);        // 都可以使用
```

 
```
     // 堆数据 - Move语义  
     let s1 = String::from("hello");
     let s2 = s1;                           // 移动所有权，不是拷贝数据
     // println!("{}", s1);                 // 错误！s1 不再有效
     println!("s2: {}", s2);
```

 
```
     // 克隆堆数据（真正拷贝）
     let s3 = String::from("world");
     let s4 = s3.clone();                   // 深度拷贝堆数据
     println!("s3: {}, s4: {}", s3, s4);    // 都可以使用
 }*
```
