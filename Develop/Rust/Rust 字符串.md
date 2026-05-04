---
source_title: Rust 字符串
categories:
- Develop
- Rust
last_modified: '2025-11-20T02:05:05Z'
---
Rust 有两种主要的字符串类型：&str（字符串切片） 和 String（可增长的字符串）。

| 特性 | &str | String |
|:---|:---|:---|
| 所有权 | 借用 | 拥有 |
| 可变性 | 不可变 | 可变 |
| 存储位置 | 栈/静态内存 | 堆 |
| 大小 | 固定 | 可增长 |
| 性能 | 轻量快速 | 需要分配 |
| 使用场景 | 函数参数、字面量 | 动态构建、修改 |
**最佳实践**：
- 函数参数优先使用 &str
- 需要修改或构建字符串时使用 String
- 避免不必要的 String 克隆
- 注意 UTF-8 编码的字符边界
- 使用 format! 宏进行复杂的字符串构建

### &str

#### 基本特性
 ```
fn str_basics() {
     // 字符串字面量是 &str 类型
     let hello: &str = "Hello, World!";
     println!("{}", hello);
 
     // &str 是固定大小的，存储在栈上或程序的只读内存中
     let static_str: &'static str = "我是静态字符串";
     println!("{}", static_str);
 
     // &str 是不可变的
     // hello[0] = 'h'; // 错误！&str 不可变
 }
```

#### 内存布局
 ```
fn str_memory_layout() {
     let s: &str = "hello";
 
     // &str 由两部分组成：
     // - 指向字符串数据的指针
     // - 长度信息
     println!("字符串: {}", s);
     println!("长度: {}", s.len());
     println!("占用内存大小: {} bytes", std::mem::size_of_val(&s));
 
     // 字符串切片
     let whole: &str = "Hello, Rust!";
     let slice: &str = &whole[0..5]; // "Hello"
     println!("切片: {}", slice);
 }
```

### String

#### 基本特性
 ```
fn string_basics() {
     // 多种创建方式
     let mut s1 = String::new();
     let s2 = String::from("Hello");
     let s3 = "World".to_string();
     let s4 = "Rust".to_owned();
 
     // 字符串增长
     let mut s = String::from("foo");
     s.push_str("bar");           // 追加字符串切片
     s.push('!');                 // 追加单个字符
     println!("{}", s);           // "foobar!"
 
     // 连接字符串
     let s1 = String::from("Hello, ");
     let s2 = String::from("World!");
     let s3 = s1 + &s2; // 注意：s1 的所有权被移动
     println!("{}", s3);
 
     // 使用 format! 宏（推荐）
     let name = "Alice";
     let age = 25;
     let greeting = format!("{} is {} years old", name, age);
     println!("{}", greeting);
 }
```

#### 内存布局
 ```
fn string_memory_layout() {
     let mut s = String::with_capacity(10);
     s.push_str("hello");
 
     println!("字符串: {}", s);
     println!("长度: {}", s.len());           // 5
     println!("容量: {}", s.capacity());     // 10
     println!("是否为空: {}", s.is_empty()); // false
 
     // String 在堆上分配内存
     println!("堆指针: {:p}", s.as_ptr());
     println!("栈上大小: {} bytes", std::mem::size_of_val(&s));
 }
```

#### 查询和检查
 ```
fn string_operations() {
     let s = String::from("Hello, 你好, 🦀!");
 
     println!("=== 基本查询 ===");
     println!("字符串: {}", s);
     println!("长度: {}", s.len());           // 字节数
     println!("字符数: {}", s.chars().count()); // Unicode 标量值数量
     println!("是否为空: {}", s.is_empty());
     println!("包含 'Hello': {}", s.contains("Hello"));
     println!("以 '!' 结尾: {}", s.ends_with('!'));
 
     println!("\n=== 索引访问 ===");
     // 直接索引可能有问题，因为 UTF-8 是变长编码
     // let c = s[0]; // 错误！不能直接索引
 
     // 正确的字符访问方式
     if let Some(first_char) = s.chars().next() {
         println!("第一个字符: {}", first_char);
     }
 
     // 字节切片（小心使用！）
     let hello_bytes = &s.as_bytes()[0..5];
     println!("前5个字节: {:?}", hello_bytes);
 }
```

#### 修改
 ```
fn string_modification() {
     let mut s = String::from("Hello Rust");
 
     println!("原始: {}", s);
 
     // 替换
     s = s.replace("Rust", "World");
     println!("替换后: {}", s);
 
     // 插入
     s.insert_str(6, "Beautiful ");
     println!("插入后: {}", s);
 
     // 删除
     s.remove(6); // 删除索引6处的字符
     println!("删除后: {}", s);
 
     // 截断
     s.truncate(5);
     println!("截断后: {}", s);
 
     // 清空
     s.clear();
     println!("清空后: '{}', 长度: {}", s, s.len());
 }
```

#### 字符串迭代
 ```
fn string_iteration() {
     let s = "Hello 你好 🦀";
 
     println!("=== 按字符迭代 ===");
     for c in s.chars() {
         println!("字符: {}", c);
     }
 
     println!("\n=== 按字节迭代 ===");
     for byte in s.bytes() {
         println!("字节: {}", byte);
     }
 
     println!("\n=== 字符位置迭代 ===");
     for (i, c) in s.char_indices() {
         println!("位置 {}: {}", i, c);
     }
 
     println!("\n=== 行迭代 ===");
     let text = "line 1\nline 2\nline 3";
     for line in text.lines() {
         println!("行: '{}'", line);
     }
 }
```

### 转换
 ```
fn conversions() {
     println!("=== &str → String ===");
     let str_slice: &str = "hello";
 
     // 多种转换方式
     let string1: String = str_slice.to_string();
     let string2: String = String::from(str_slice);
     let string3: String = str_slice.to_owned();
 
     println!("{} {} {}", string1, string2, string3);
 
     println!("\n=== String → &str ===");
     let string_obj: String = String::from("world");
 
     // String 可以自动解引用为 &str
     let slice1: &str = &string_obj;
     let slice2: &str = string_obj.as_str();
 
     println!("{} {}", slice1, slice2);
 
     // 函数参数中的自动转换
     print_str(&string_obj);  // String 自动转为 &str
     print_str("literal");    // &str 字面量
 }
 
 fn print_str(s: &str) {
     println!("接收到的字符串: {}", s);
 }
```

### 性能考虑
 ```
fn performance_considerations() {
     println!("=== 性能考虑 ===");
 
     // 1. 使用 with_capacity 避免重复分配
     let mut s = String::with_capacity(100);
     for i in 0..10 {
         s.push_str(&format!("{} ", i));
     }
     println!("预分配字符串: {}", s);
 
     // 2. &str 比 String 更轻量
     fn process_text(text: &str) -> usize {
         text.len()  // 使用 &str 避免不必要的复制
     }
 
     let string_data = String::from("some data");
     let result1 = process_text(&string_data);  // 借用
     let result2 = process_text("static data"); // 直接使用字面量
     println!("结果: {}, {}", result1, result2);
 
     // 3. 字符串复制的成本
     let original = String::from("expensive string");
     let copy1 = original.clone();      // 深拷贝 - 昂贵
     let copy2 = &original;             // 借用 - 廉价
     println!("原始: {}, 拷贝1: {}, 拷贝2: {}", original, copy1, copy2);
 }
```

### 应用场景

#### 场景1：配置文件解析
 ```
fn config_parsing() {
     println!("=== 配置文件解析 ===");
 
     let config_text = "host=localhost\nport=8080\ntimeout=30";
     let mut config = std::collections::HashMap::new();
 
     for line in config_text.lines() {
         if let Some(equal_pos) = line.find('=') {
             let key = &line[..equal_pos];
             let value = &line[equal_pos + 1..];
             config.insert(key.to_string(), value.to_string());
         }
     }
 
     println!("配置: {:?}", config);
 }
```

#### 场景2：文本处理
 ```
fn text_processing() {
     println!("\n=== 文本处理 ===");
 
     let text = "Rust是一种系统编程语言，注重安全性和性能。";
 
     // 统计信息
     let char_count = text.chars().count();
     let word_count = text.split_whitespace().count();
     let byte_count = text.len();
 
     println!("文本: {}", text);
     println!("字符数: {}, 单词数: {}, 字节数: {}", char_count, word_count, byte_count);
 
     // 提取子字符串
     if let Some(pos) = text.find("系统编程") {
         let substring = &text[pos..pos + "系统编程".chars().count()];
         println!("找到关键词: {}", substring);
     }
 }
```

#### 场景3：构建动态字符串
 ```
fn dynamic_string_building() {
     println!("\n=== 动态字符串构建 ===");
 
     let names = vec!["Alice", "Bob", "Charlie"];
     let ages = vec![25, 30, 35];
 
     // 使用 String 构建复杂输出
     let mut output = String::new();
 
     for (name, age) in names.iter().zip(ages.iter()) {
         output.push_str(&format!("{} ({}岁)\n", name, age));
     }
 
     println!("人员列表:\n{}", output);
 
     // 使用 join 更高效
     let joined = names.join(", ");
     println!("所有名字: {}", joined);
 }
```

### 常见陷阱
 ```
fn common_pitfalls() {
     println!("=== 常见陷阱 ===");
 
     let s = "🦀 Hello 你好";
 
     // 陷阱1：错误的字符串切片
     // let slice = &s[0..1]; // 恐慌！在字符边界外切片
 
     // 正确的切片方式
     if let Some(c) = s.chars().next() {
         println!("第一个字符: {}", c);
     }
 
     // 陷阱2：误用字节位置
     let hello = "你好";
     // println!("{}", &hello[0..1]); // 错误！
     println!("正确切片: {}", &hello[0..3]); // "你" 的 UTF-8 编码占3字节
 
     // 最佳实践：使用字符迭代
     for c in hello.chars() {
         print!("{} ", c);
     }
     println!();
 
     // 陷阱3：不必要的 String 克隆
     let source = String::from("data");
     fn process(s: &str) { println!("处理: {}", s); }
 
     process(&source);        // 好：借用
     // process(source.clone()); // 不好：不必要的克隆
 }
```

### 示例
 ```
fn comprehensive_example() {
     println!("=== 综合示例：简单的文本分析器 ===");
 
     let text = String::from(
         "Rust 是一种令人惊叹的编程语言。\n\
          It provides memory safety without garbage collection.\n\
          🦀 螃蟹是 Rust 的吉祥物！"
     );
 
     analyze_text(&text);
 }
 
 fn analyze_text(text: &str) {
     println!("分析文本:\n{}\n", text);
 
     // 基本统计
     let lines: Vec<&str> = text.lines().collect();
     let total_chars = text.chars().count();
     let total_bytes = text.len();
     let total_words = text.split_whitespace().count();
 
     println!("统计信息:");
     println!("- 行数: {}", lines.len());
     println!("- 字符数: {}", total_chars);
     println!("- 字节数: {}", total_bytes);
     println!("- 单词数: {}", total_words);
 
     // 行分析
     println!("\n行分析:");
     for (i, line) in lines.iter().enumerate() {
         let line_chars = line.chars().count();
         let line_words = line.split_whitespace().count();
         println!("行 {}: {} 字符, {} 单词", i + 1, line_chars, line_words);
     }
 
     // 字符频率
     println!("\n字符频率:");
     let mut char_frequency = std::collections::HashMap::new();
     for c in text.chars() {
         if c.is_alphabetic() {
             *char_frequency.entry(c).or_insert(0) += 1;
         }
     }
 
     let mut sorted_chars: Vec<_> = char_frequency.iter().collect();
     sorted_chars.sort_by(|a, b| b.1.cmp(a.1));
 
     for (char, count) in sorted_chars.iter().take(5) {
         println!("'{}': {} 次", char, count);
     }
 }
 
 fn main() {
     str_basics();
     str_memory_layout();
     string_basics();
     string_memory_layout();
     conversions();
     string_operations();
     string_modification();
     string_iteration();
     performance_considerations();
     config_parsing();
     text_processing();
     dynamic_string_building();
     common_pitfalls();
     comprehensive_example();
 }
```
