---
source_title: Rust 基准测试
categories:
- Develop
- Rust
last_modified: '2025-12-16T14:21:02Z'
---
Criterion.rs 是 Rust 的统计驱动基准测试库，提供：
- 精确的性能测量
- 统计显著性分析
- 图表化报告
- 与 Cargo 无缝集成

## 🚀 安装配置

### 1. 添加依赖
 ```

# Cargo.toml
[package]
name = "benchmark"
version = "0.1.0"
edition = "2021"
description = "性能测试套件"
license = "MIT OR Apache-2.0"
publish = false                                  # 不发布基准测试 crate
[dev-dependencies]
criterion = { version = "0.5", features = ["html_reports"] }
[[bench]]
name = "main"
harness = false                                  # 禁用默认的 bench harness
[[bench]]
name = "find_utf8_boundary"                      # 单独文件，且需要通过 --bench 执行。
harness = false
```

### 2. 项目结构
 ```
benchmark/
 ├── Cargo.toml
 ├── benches/
 │   ├── main.rs
 │   └── find_utf8_boundary.rs
 └── target/
      └── criterion/                          # 自动生成的报告
```

## 🔧 基础用法

### 最简单的基准测试
 ```
// benches/main.rs
 use criterion::{black_box, criterion_group, criterion_main, Criterion};
 
 fn fibonacci(n: u64) -> u64 {
     match n {
         0 => 1,
         1 => 1,
         n => fibonacci(n-1) + fibonacci(n-2),
     }
 }
 
 fn bench_fib(c: &mut Criterion) {
     c.bench_function("fib 20", |b| {
         b.iter(|| black_box(fibonacci(black_box(20))))
     });
 }
 
 criterion_group!(benches, bench_fib);
 criterion_main!(benches);
```

### 运行基准测试
 ```

# 运行默认基准测试
 cargo bench
 
 # 运行特定的基准测试
 cargo bench --bench find_utf8_boundary
```

## 📊 基准测试组

### 创建测试组
 ```
fn bench_group(c: &mut Criterion) {
     let mut group = c.benchmark_group("字符串处理");
 
     // 配置组级别的设置
     group.sample_size(1000)                           // 样本数量
          .measurement_time(Duration::from_secs(10));  // 测量时间
 
     group.bench_function("to_uppercase", |b| {
         let s = "hello world";
         b.iter(|| s.to_uppercase())
     });
 
     group.bench_function("to_lowercase", |b| {
         let s = "HELLO WORLD";
         b.iter(|| s.to_lowercase())
     });
 
     group.finish();                                   // 必须调用 finish
 }
```

### 带参数的基准测试
 ```
fn bench_with_size(c: &mut Criterion) {
     let mut group = c.benchmark_group("向量操作");
 
     for size in [10, 100, 1000, 10000].iter() {
         group.bench_with_input(
             format!("vec_sort_{}", size), 
             size, 
             |b, &size| {
                 let mut v = (0..size).rev().collect::>();
                 b.iter(|| v.sort());
             }
         );
     }
 
     group.finish();
 }
```

## ⚡ 高级功能

### 1. 对比基准测试
 ```
fn bench_comparison(c: &mut Criterion) {
     let mut group = c.benchmark_group("算法对比");
 
     group.bench_function("快速排序", |b| {
         b.iter(|| quick_sort(&mut test_data()))
     });
 
     group.bench_function("归并排序", |b| {
         b.iter(|| merge_sort(&mut test_data()))
     });
 
     group.bench_function("标准库排序", |b| {
         b.iter(|| test_data().sort())
     });
 }
```

### 2. 自定义测量
 ```
use criterion::{BenchmarkId, Criterion, Throughput};
 
 fn bench_throughput(c: &mut Criterion) {
     let mut group = c.benchmark_group("吞吐量测试");
 
     for size in [1, 10, 100, 1000].iter() {
         group.throughput(Throughput::Bytes(*size as u64));  // 设置吞吐量单位
 
         group.bench_with_input(
             BenchmarkId::new("处理数据", size), 
             size, 
             |b, &size| {
                 let data = vec![0u8; size];
                 b.iter(|| process_data(&data));
             }
         );
     }
 }
```

### 3. 异步基准测试
 ```
use criterion::async_executor::FuturesExecutor;
 use tokio::runtime::Runtime;
 
 fn bench_async(c: &mut Criterion) {
     c.bench_function("async_function", |b| {
         b.to_async(FuturesExecutor).iter(|| async {
             async_operation().await
         });
     });
 }
```

## 📈 报告和分析

### 查看报告
 ```

# 运行后，报告生成在：
 target/criterion/report/index.html
```

### 配置报告选项
 ```
criterion_group!{
     name = benches;
     config = Criterion::default()
         .with_plots()  // 生成图表
         .with_output_color()  // 彩色输出
         .sample_size(100)  // 样本大小
         .measurement_time(Duration::from_secs(5))  // 测量时间
         .warm_up_time(Duration::from_secs(1));  // 预热时间
     targets = bench_fib, bench_group
 }
```

## 🏆 最佳实践

### 1. 避免常见的坑
 ```
// ❌ 错误：状态累积
 fn bad_bench(c: &mut Criterion) {
     let mut v = Vec::new();
     c.bench_function("bad", |b| {
         b.iter(|| v.push(1));  // v 会越来越大
     });
 }
 
 // ✅ 正确：每次迭代重新初始化
 fn good_bench(c: &mut Criterion) {
     c.bench_function("good", |b| {
         b.iter(|| {
             let mut v = Vec::new();  // 每次重新创建
             v.push(1);
             black_box(v);
         });
     });
 }
```

### 2. 处理昂贵初始化
 ```
fn expensive_setup_bench(c: &mut Criterion) {
     c.bench_function("expensive", |b| {
         // 昂贵的初始化放在 iter 外面
         let data = load_large_dataset();  // 只执行一次
 
         b.iter(|| {
             // 只测试处理部分
             process(&data)
         });
     });
 }
```

### 3. 使用 BenchmarkId
 ```
fn parameterized_bench(c: &mut Criterion) {
     let mut group = c.benchmark_group("参数化测试");
 
     for param in &[1, 2, 4, 8, 16] {
         group.bench_with_input(
             BenchmarkId::from_parameter(param),  // 自动生成名称
             param,
             |b, &param| {
                 b.iter(|| operation(param));
             }
         );
     }
 }
```

## 🔍 常见问题

### Q1: 为什么我的基准测试结果不稳定？

可能原因和解决方案：
1. 系统负载过高：关闭其他程序
1. 编译器优化：正确使用 ```

black_box
```

# 缓存效应：使用足够大的数据集

# 测量时间太短：增加 ```
measurement_time
```

### Q2: 如何比较不同提交的性能？
 ```

# 保存当前基准结果
 cargo bench -- --save-baseline master
 
 # 修改代码后比较
 cargo bench -- --baseline master
```

### Q3: 如何测试内存使用？

Criterion 主要测量时间性能，内存测试需要其他工具：
 ```

# 使用 valgrind
 valgrind --tool=massif cargo bench
 
 # 或使用 heaptrack
 heaptrack cargo bench
```

### Q4: 在 CI 中运行基准测试
 ```

# GitHub Actions 示例
 name: Benchmark
 on: [push]
 jobs:
   bench:
     runs-on: ubuntu-latest
     steps:
       - uses: actions/checkout@v2
       - uses: actions-rs/toolchain@v1
         with:
           toolchain: stable
       - run: cargo bench -- --verbose
```

## 📚 完整示例
 ```
// benches/complete_example.rs
 use criterion::{black_box, criterion_group, criterion_main, Criterion, BenchmarkId, Throughput};
 use std::time::Duration;
 
 // 要测试的函数
 fn process_chunk(data: &[u32]) -> u32 {
     data.iter().sum()
 }
 
 fn bench_comprehensive(c: &mut Criterion) {
     // 基准测试组 1：不同大小的数据处理
     let mut size_group = c.benchmark_group("不同数据大小");
     size_group.sample_size(1000);
 
     for size in [100, 1000, 10000].iter() {
         size_group.throughput(Throughput::Elements(*size as u64));
 
         size_group.bench_with_input(
             BenchmarkId::new("求和", size),
             size,
             |b, &size| {
                 let data: Vec = (0..size).collect();
                 b.iter(|| {
                     black_box(process_chunk(&data))
                 });
             }
         );
     }
     size_group.finish();
 
     // 基准测试组 2：不同算法对比
     let mut algo_group = c.benchmark_group("算法对比");
     algo_group.warm_up_time(Duration::from_millis(500));
 
     algo_group.bench_function("算法A", |b| {
         let data = create_test_data(1000);
         b.iter(|| algorithm_a(&data));
     });
 
     algo_group.bench_function("算法B", |b| {
         let data = create_test_data(1000);
         b.iter(|| algorithm_b(&data));
     });
 
     algo_group.finish();
 }
 
 criterion_group!{
     name = benches;
     config = Criterion::default()
         .with_plots()
         .measurement_time(Duration::from_secs(10));
     targets = bench_comprehensive
 }
 
 criterion_main!(benches);
```

## 🔗 资源
- 官方文档
- GitHub 仓库
- API 文档

---

## 💡 小贴士
1. 使用 ```

--verbose

``` 参数查看详细输出
1. 定期运行基准测试追踪性能回归
1. 在稳定环境中运行获取可靠结果
1. 保存基线方便后续比较
