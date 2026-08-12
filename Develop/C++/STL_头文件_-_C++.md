---
source_title: STL 头文件 - C++
categories:
- C++
- Develop
last_modified: '2025-06-05T01:36:55Z'
---
C++ STL（标准模板库）是一套功能强大的 C++ 模板类，提供了通用的模板类和函数，涵盖了输入/输出、容器、算法、多线程、正则表达式等。这些模板类和函数可以实现多种流行和常用的算法和数据结构，如向量、链表、队列、栈。

C++ 标准库可以分为两部分：
1. 标准函数库：由通用的、独立的、不属于任何类的函数组成的。函数库继承自 C 语言。C++ 标准库包含了所有的 C 标准库，为了支持类型安全，做了一定的添加和修改。
1. 面向对象类库：类及其相关函数的集合。

### C++ Standard Library Overview

#### 输入输出 (Input/Output)

| Library Name | Description | Common Functions/Features |
| 标准输入输出流 | cin, cout, cerr, clog |
| 文件输入输出流 | ifstream, ofstream, fstream, open(), close(), read(), write() |
| 字符串流 | istringstream, ostringstream, stringstream |
| 输入输出流格式化 | setw(), setprecision(), fixed, scientific, setfill() |
| C 风格输入输出 | printf(), scanf(), fopen(), fclose(), fgets(), fputs() |

#### 字符串 (Strings)

| Library Name | Description | Common Functions/Features |
| 标准字符串类 | size(), length(), append(), insert(), erase(), find(), substr(), compare(), c_str() |
| 正则表达式 | regex, regex_match(), regex_search(), regex_replace() |
| / | C 风格字符串处理 | strcpy(), strcat(), strlen(), strcmp(), strstr(), memcpy(), memmove() |
| / | 宽字符处理 | wcslen(), wcscpy(), wcscmp() |
| / | 字符处理 | isalpha(), isdigit(), isspace(), toupper(), tolower() |

#### 容器 (Containers)

| Library Name | Description | Common Functions/Features |
| 定长数组容器 | size(), front(), back(), operator[], at() |
| 动态数组容器 | push_back(), pop_back(), size(), capacity(), resize(), insert(), erase(), operator[], at(), begin(), end() |
| 双端队列容器 | push_front(), push_back(), pop_front(), pop_back(), size(), operator[], at() |
| 双向链表容器 | push_front(), push_back(), pop_front(), pop_back(), insert(), erase(), sort(), merge() |
| 单向链表容器 | push_front(), pop_front(), insert_after(), erase_after(), sort() |
| 栈容器适配器 | push(), pop(), top(), empty(), size() |
| 队列容器适配器 | push(), pop(), front(), back(), empty(), size() |
| 优先队列容器适配器 | push(), pop(), top(), empty(), size() |
| 集合容器（基于平衡二叉树） | insert(), erase(), find(), count(), lower_bound(), upper_bound() |
| 无序集合容器（基于哈希表） | insert(), erase(), find(), count(), bucket_count() |
| 映射容器（键值对，基于平衡二叉树） | insert(), erase(), find(), operator[], at(), count() |
| 无序映射容器（基于哈希表） | insert(), erase(), find(), operator[], at(), count(), bucket_count() |
| 二进制位容器 | set(), reset(), flip(), test(), count(), to_string(), to_ulong(), to_ullong() |

#### 数学与数值 (Numerics)

| Library Name | Description | Common Functions/Features |
| 数值操作（如累计、内积等） | accumulate(), iota(), inner_product(), partial_sum(), adjacent_difference() |
| 复数运算 | complex, real(), imag(), abs(), arg(), conj(), polar() |
| 数组类及相关操作 (用于数值计算) | valarray, apply(), sum(), min(), max() |
| / | 数学函数 | sin(), cos(), tan(), sqrt(), pow(), log(), exp(), fabs(), ceil(), floor() |
| 随机数生成 | default_random_engine, uniform_int_distribution<>, normal_distribution<> |

#### 算法 (Algorithms)

| Library Name | Description | Common Functions/Features |
| 常用算法 | sort(), find(), binary_search(), min(), max(), copy(), transform(), reverse(), accumulate(), for_each(), count(), find_if(), permutation() |

#### 时间与日期 (Time & Date)

| Library Name | Description | Common Functions/Features |
| / | C 风格时间处理 | time(), clock(), difftime(), strftime(), localtime() |
| 时间库 (高精度) | duration, time_point, system_clock, steady_clock, high_resolution_clock |

#### 多线程 (Multithreading)

| Library Name | Description | Common Functions/Features |
| 多线程支持 | thread, join(), detach(), get_id(), sleep_for(), sleep_until() |
| 互斥量 | mutex, lock(), unlock(), try_lock(), recursive_mutex, timed_mutex |
| 条件变量 | condition_variable, wait(), notify_one(), notify_all() |
| 异步编程支持 | future, promise, packaged_task, async() |
| 原子操作 | atomic, load(), store(), exchange(), fetch_add() |

#### 内存管理 (Memory Management)

| Library Name | Description | Common Functions/Features |
| 智能指针及动态内存管理 | unique_ptr, shared_ptr, weak_ptr, make_unique(), make_shared(), allocator |
| 动态内存分配 | new, delete, nothrow |

#### 类型支持 (Type Support)

| Library Name | Description | Common Functions/Features |
| 类型特性 (编译期类型信息) | is_integral<>, is_floating_point<>, is_same<>, enable_if<>, remove_reference<> |
| 运行时类型识别 (RTTI) | typeid, type_info |
| / | 定长整数类型 | int8_t, uint16_t, int32_t, uint64_t |
| 数值类型属性 | numeric_limits<> (min(), max(), epsilon()) |
| / | C 风格整数极限 | INT_MAX, LONG_MIN, CHAR_BIT |
| / | C 风格浮点极限 | FLT_MAX, DBL_MIN, LDBL_EPSILON |

#### 迭代器 (Iterators)、函数对象 (Function Objects)、异常处理 (Exception Handling)

| Library Name | Description | Common Functions/Features |
| 迭代器及相关工具 | iterator_traits, distance(), advance(), begin(), end(), prev(), next(), inserter, back_inserter, front_inserter |
| 定义函数对象及相关工具 | function, bind(), plus<>, minus<>, mem_fn(), ref(), cref() |
| 异常处理基类及相关工具 | exception, what(), terminate(), current_exception() |
| 常用异常类 | logic_error, runtime_error, out_of_range, invalid_argument |

#### 其他工具 (Utilities)

| Library Name | Description | Common Functions/Features |
| 通用工具 | pair, make_pair(), move(), forward(), swap() |
| 本地化支持 | locale, imbue() |
| / | 断言 | assert() |
| / | C 风格常用工具 | rand(), srand(), abs(), labs(), malloc(), free(), exit(), system() |
| / | C 风格信号处理 | signal(), raise() |
| / | C 风格非局部跳转 | setjmp(), longjmp() |
| / | C 风格可变参数函数支持 | va_list, va_start(), va_arg(), va_end() |
| / | C 风格常用定义 | nullptr_t, size_t, ptrdiff_t, offsetof() |
| 初始化列表支持 | initializer_list |
| 元组容器 | tuple, make_tuple(), get<>(), tie() |
| (C++17) | 类型安全的联合体 | variant, get<>(), get_if(), holds_alternative() |
| (C++17) | 可选值容器 | optional, has_value(), value(), value_or() |
| (C++17) | 类型安全的任意类型容器 | any, any_cast<>(), has_value(), type() |
| (C++17) | 文件系统操作 | path, exists(), is_directory(), create_directory(), remove() |
| (C++20) | 获取源码位置信息 | source_location |
| (C++20) | 格式化库 | format() |
| <span> (C++20) | 非拥有型连续序列视图 | span |
| (C++20) | 范围库，改进的算法和视图 | views::filter, views::transform, ranges::sort() |
| (C++20) | 概念，用于模板参数约束 | concept, requires |
| (C++20) | 协程支持 | coroutine_handle, co_await, co_yield, co_return |
| (C++20) | 三路比较运算符支持 | strong_ordering, weak_ordering, partial_ordering, operator<=> |
| (C++20) | C++特性测试宏 | Feature test macros like __cpp_lib_ranges |

#### 已弃用及替换

| Library Name | Description | Common Functions/Features | Memo |
| 字符编码转换 | codecvt_utf8, codecvt_utf16 | C++17 已弃用，计划C++26删除，暂无替换方案 |
