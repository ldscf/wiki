---
source_title: Python 迭代器
categories:
- Develop
- Python
last_modified: '2025-07-21T06:49:34Z'
---
Python 中的迭代器（Iterator）和生成器（Generator，迭代器的一种简洁写法）都是用于逐个遍历数据的工具，遵循迭代协议（Iterator Protocol）。生成器支持惰性计算，尤其适合处理大量数据或流式数据，避免一次性加载所有内容带来的内存压力。

| 特性 | 迭代器 | 生成器 |
|:---|:---|:---|
| 编写方式 | 需要实现 __iter__ 和 __next__ | 使用 yield 或生成器表达式构建 |
| 代码简洁度 | 较复杂 | 非常简洁 |
| 内存占用 | 一般较小 | 更小（惰性生成） |
| 状态维护 | 需维护状态变量 | 自动保存上下文状态 |
__TOC__

### 迭代器

迭代器是一个实现了两个方法的对象：
- __iter__()：返回迭代器对象自身
- __next__()：返回下一个元素，如果没有更多元素，则抛出 StopIteration

自定义迭代器
 ```
class MyIterator:
    def __init__(self, max):
        self.max = max
        self.current = 0
    def __iter__(self):
        return self
    def __next__(self):
        if self.current < self.max:
            num = self.current
            self.current += 1
            return num
        else:
            raise StopIteration
it = MyIterator(3)
for i in it:
    print(i)
输出：
0
1
2
```

列表、元组、字典（list/tuple/dict）均为可迭代对象（Iterable），但本身不是迭代器（Iterator），因为只是实现了 __iter__() 方法，并没有实现 __next__() 方法。
 ```
lst = [1, 2, 3]
print(hasattr(lst, '__iter__'))   # True
print(hasattr(lst, '__next__'))   # False
it = iter(lst)                    # 
print(hasattr(it, '__iter__'))    # True
print(hasattr(it, '__next__'))    # True
```

### 生成器

生成器是一种特殊的迭代器，按需计算（惰性计算）。构造生成器有两种方法：
- 生成器函数（使用 yield）
- 生成器表达式（类似列表推导式）
 ```
def my_generator():
    yield 1
    yield 2
    yield 3
gen = my_generator()
for val in gen:
    print(val)
输出：
1
2
3
```
 ```
gen_exp = (x + 1 for x in range(3))
for val in gen_exp:
    print(val)
输出：
1
2
3
```

#### 生成器优点
- 惰性求值：只在需要时才计算，节省内存
- 可用于无限序列：如斐波那契数列、文件读取
- 适合处理大数据流：例如读取大文件、网络数据等
 ```

# 处理大文件
 def read_lines(filename):
     with open(filename) as f:
         for line in f:
             yield line.strip()
 
 # 处理到哪一行时，才把这一行读到内存
 for line in read_lines('bigfile.txt'):
     print(line)
```
 ```
#生成器表达式（惰性计算，只计算符合条件的 str(tags[tag]）
result = next(
    (str(tags[tag]) for tag in ['A', 'B', 'C'] if tag in tags),
    None
)
#列表推导式（构造所有元素，会计算 'A', 'B', 'C' 的 str(tags[tag]）
result = next(
    [str(tags[tag]) for tag in ['A', 'B', 'C'] if tag in tags],
    None
)
```

| 特性 | 生成器表达式 | 列表推导式 |
|:---|:---|:---|
| 计算方式 | 惰性计算，按需生成 | 立即构造全部结果 |
| 性能 | 更高效，遇到第一个匹配就返回 | 整个表达式全部求值后再返回第一个元素 |
| 内存消耗 | 较低 | 较高 |
| 推荐使用场景 | 查找第一个满足条件的元素（短路查找） | 要使用全部结果或只在结果很少时可接受 |
