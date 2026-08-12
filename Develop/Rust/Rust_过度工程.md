---
source_title: Rust 过度工程
categories:
- Develop
- Rust
last_modified: '2025-12-11T07:52:06Z'
---
简洁 vs 过度工程

你的观点完全正确：这时候简洁更重要！

### 让我们回归常识
```
 // 你的原始代码：✅ 简洁明了
 let mut total_result = 0;
 for _ in 0..1000 {
```

     let result = find_utf8_boundary(black_box(&data), black_box(index));

     total_result += result;  // 这就是最直接的写法
```
 }
 black_box(total_result)
```

#### 实际情况分析
```
 // 现实中的数值：
 数据大小: 1MB = 1,048,576 字节
 每次结果最大: 1,048,576
 循环1000次累计最大: 1,048,576,000
```
 
```
 // 64位 usize 最大值: 18,446,744,073,709,551,615
 // 32位 usize 最大值: 4,294,967,295
```
 
```
 // 结论：
 64位系统：安全（差17000多倍）
 32位系统：有风险（但谁还用32位跑Rust基准测试？）
```

### 工程实践的平衡

#### Rust社区的实际情况
1. 几乎没人用32位系统跑基准测试
1. 基准测试默认用release模式（无溢出检查）
1. 实际数值远小于理论最大值

#### 简洁代码的价值
```
 // 版本A：简洁直接（你的代码）
 total_result += result;
```
 
```
 // 版本B：过度防御
 total_result = total_result.checked_add(result)
```

     .unwrap_or_else(|| {

         eprintln!("溢出警告");

         usize::MAX

     });
 
```
 // 哪个更好读？哪个更容易维护？
```

### 实际建议：就这么用！

坚持你的简洁写法：
```
 group.bench_function(
```

     &format!("find_utf8_boundary_at_{}", index),

     |b| b.iter(|| {

         let mut total_result = 0;

         for _ in 0..1000 {

             let result = find_utf8_boundary(black_box(&data), black_box(index));

             total_result += result;  // ✅ 简洁、清晰、实用

         }

         black_box(total_result)

     })
```
 );
```

### 如果真的担心，一行防御就够了
```
 // 如果真的不放心，可以加这一行（在循环前）：
 debug_assert!(data.len() * 1000 < usize::MAX, "可能溢出");
```
 
```
 // 或者更简单的：
 if cfg!(debug_assertions) {
```

     // debug模式才检查

     assert!(data.len() * 1000 < usize::MAX, "调试时检查溢出");
```
 }
```

### 看看真实项目怎么做

#### 以标准库为例
```
 // 很多标准库代码也用 +=
 fn sum_iterator>(iter: T) -> usize {
```

     let mut total = 0;

     for item in iter {

         total += item;  // 标准库也这样写！

     }

     total
```
 }
```
 
```
 // 只有在确实可能溢出且需要特殊处理时才用 wrapping_add
```

### 性能对比（几乎无差别）

在 release 模式下：
```
 // 两种写法生成的汇编几乎一样
 total += x;          // add rax, rbx
 total.wrapping_add(x) // add rax, rbx (一样的指令)
```

### 7. 最终决策：信任常识

你的直觉是对的：在基准测试这种特定场景：
1. 数据规模可控
1. 运行环境明确
1. 代码需要简洁
1. 过度防御反而降低可读性

### 简洁的黄金法则
```
 // 原则：先写简洁代码，有问题再优化
 fn benchmark_code() {
```

     // 第1步：写最直观的代码 ✅

     let mut total = 0;

     for _ in 0..1000 {

         total += compute();

     }
 
     // 第2步：如果真有溢出问题，再改

     // （但目前看，没有）
```
 }
```

### 结论：坚持你的简洁写法！
```
 // 这就是好代码：
 total_result += result;
```
 
```
 // 理由：
 1. ✅ 简洁明了
 2. ✅ 符合直觉
 3. ✅ 实际安全（你的场景）
 4. ✅ 易于维护
 5. ✅ 性能无差异
```
 
```
 // 不要为了"可能"的问题牺牲可读性
 // 保持代码简洁，有问题再解决
```

就这么写，没问题！ 简洁是 Rust 的美德之一。

*from DeepSeek-V3.2 2025/12/11*
