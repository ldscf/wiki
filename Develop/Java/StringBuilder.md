---
source_title: StringBuilder
categories:
- Develop
- Java
last_modified: '2024-11-04T02:01:53Z'
---
String、StringBuffer、StringBuilder 底层用 char[]，都是final类，不允许被继承，这样设计主要是从性能和安全性上考虑的。
- String 构建的字符串对象，其内容理论上是不能被改变的。但很多时候需要改变字符串的内容的，如常见的字符串拼接，一般使用 "+" 来拼接，实际上会创建一个新的字符串，造成执行效率低下
- StringBuffer 是一种可变的字符串类。每个 StringBuffer 的类对象都能够存储指定容量的字符串，如果字符串的长度超过了 StringBuffer 对象的容量空间，则该对象的容量会自动扩大
- 在 Java 5 中提出 StringBuilder 类，与 StringBuffer 的用法几乎完全一样，虽然不是线程安全，但执行效率更快

#### 字符串拼接测试

当拼接次数较多时，String 出现非线性增长，应该是直接触发了 GC 机制。

| 对象 | 时间(毫秒) |
|:---|:---|
| +10 万次字符串拼接 |
| String | 1576 |
| StringBuffer | 4 |
| StringBuilder | 3 |
| 对象 | 时间(毫秒) |
|:---|:---|
| +100 万次字符串拼接 |
| String | 92904 |
| StringBuffer | 33 |
| StringBuilder | 20 |
