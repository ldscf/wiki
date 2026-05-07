---
source_title: STL - C++
categories:
- C++
- Develop
last_modified: '2024-10-06T11:51:18Z'
---
C++ STL（标准模板库）是一套功能强大的 C++ 模板类，提供了通用的模板类和函数，涵盖了输入/输出、容器、算法、多线程、正则表达式等。这些模板类和函数可以实现多种流行和常用的算法和数据结构，如向量、链表、队列、栈。

| 组件 | 描述 |
|:---|:---|
| 容器（Containers） | 容器是用来管理某一类对象的集合。C++ 提供了各种不同类型的容器，比如 deque、list、vector、map 等。 |
| 算法（Algorithms） | 算法作用于容器。它们提供了执行各种操作的方式，包括对容器内容执行初始化、排序、搜索和转换等操作。 |
| 迭代器（iterators） | 迭代器用于遍历对象集合的元素。这些集合可能是容器，也可能是容器的子集。 |

### vector

向量（Vector）是一个封装了动态大小数组的顺序容器（Sequence Container）。能够存放各种类型的对象。

| 对象 | 描述 |
|:---|:---|
| 顺序序列 | 顺序容器中的元素按照严格的线性顺序排序。可以通过元素在序列中的位置访问对应的元素 |
| 动态数组 | 支持对序列中的任意元素进行快速直接访问，甚至可以通过指针算述进行该操作。提供了在序列末尾相对快速地添加/删除元素的操作 |
| Allocator-aware | 能够感知内存分配器的 Allocator-aware，容器使用一个内存分配器对象来动态地处理它的存储需求 |
| 基本函数(#include) | 描述 |
|:---|:---|
| vector v1(int n, const t& t) | 创建一个vector v1，元素个数为n，且值均为t |
| int size() const | 返回向量中元素的个数 |
| for (int v : v1) | 遍历 vector v1，替代迭代器（vector::iterator it......*it）写法 |

### stack

栈(Stack)是一种线性存储结构，遵守“先进后出"(First In Last Out)的原则，简称FILO结构。只能在栈顶进行插入和删除操作。

| 对象 | 描述 |
|:---|:---|
| 栈顶与栈底 | 允许元素插入与删除的一端称为栈顶，另一端称为栈底 |
| 压栈 | 栈的插入操作，叫做进栈，也称压栈、入栈 |
| 弹栈 | 栈的删除操作，也叫做出栈 |
| 基本函数(#include) | 描述 |
|:---|:---|
| stack s | 创建一个stack |
| s.empty() | 如果栈为空则返回true, 否则返回false |
| s.size() | 返回栈中元素的个数 |
| s.top() | 返回栈顶元素, 但不删除该元素 |
| s.pop() | 弹出栈顶元素, 但不返回其值 |
| s.push() | 将元素压入栈顶 |

### map

一种键值对关联容器（key-value pairs）。

| 对象 | 查找效率 | 顺序性 | 底层 | 场景 |
|:---|:---|:---|:---|:---|
| map | O(logN) | 有序 | 红黑树 | 有序访问，范围查找 |
| unordered_map | O(1) | 无序 | 哈希表 | 频繁查找，无序访问 |
    m.first, m.second

| 基本函数(#include) | 描述 |
|:---|:---|
| map m1 = {{"apple", 1}, {"banana", 2}}; | 创建一个 map |
| m1["apple"] = 3 | 赋值 |
| m1["apple"] | 查找 |
| m1.count("apple") > 0 | 检查键是否存在 |
| for (auto& m : m1) | 遍历 |
P.S. 有一个不寻常的现象，当使用 const 定义 map 时，无法使用 m1[1] 取值，其他如遍历等正常。
 ```
const std::unordered_map LOG_LEVEL = {{0, "DEBUG"}, {3, "INFO"}};
LOG_LEVEL[0] -> error: passing ‘const std::unordered_map >’ as ‘this’ argument discards qualifiers [-fpermissive]
```
