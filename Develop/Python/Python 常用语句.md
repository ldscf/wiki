---
source_title: Python 常用语句
categories:
- Develop
- Python
last_modified: '2025-03-21T05:22:38Z'
---

### Dict

Python 字典的顺序问题取决于 Python 的版本：
- 3.7 及以后版本，字典保持插入顺序，语言规范特性
- 3.6 CPython 的字典保持插入顺序，非语言规范的一部分，不应该依赖这个行为，因为其他（如 Jython 或 IronPython）可能不保持插入顺序
- 3.5 及更早版本，字典不保持插入顺序。遍历字典时，键值对的顺序是不可预测的

#### 第一个 Key
```
 # d1={'a':1, 'b':2}
 next(iter(d1))
```

#### 最后一个 Key
```
 list(d1.keys())[-1]
```

#### defaultdict

内置字典 dict 的一个子类，可以自动为不存在的键提供一个默认值，从而避免了在访问字典时常见的 KeyError 异常。
 ```
from collections import defaultdict
dd1 = defaultdict(int)
dd1['a'] += 1   # 无论 a 是否已经存在，都可以直接累加
dd2 = defaultdict(list)
dd2['a'].append(1)   # 自动创建一个空列表
dd3 = defaultdict(lambda: {"total": 0, "ok": 0, "err": 0})
dd3['a']['total'] += 1    # 键不存在时，自动调用 lambda 函数，返回字典
```

### lambda

lambda 表达式（也称为匿名函数）是一种创建小型、一次性使用的函数的简洁方式。通常用于需要一个简单函数作为参数的场景，例如在 map()、filter()、sorted() 等高阶函数中。

**语法**
```
 lambda arguments: expression
```
- arguments: 参数列表，可以有零个或多个参数，用逗号分隔
- expression: 单个表达式，求值并返回

**Sample**
 ```
add = lambda x, y: x + y
result = add(5, 3)
max = lambda a, b: a if a > b else b
max(10, 5)

# map
numbers = [1, 2, 3, 4, 5]
squared_numbers = list(map(lambda x: x * x, numbers))
print(squared_numbers)  # 输出: [1, 4, 9, 16, 25]

# filter
numbers = [1, 2, 3, 4, 5, 6]
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers)  # 输出: [2, 4, 6]

# sorted()，按元素的长度排序
words = ["apple", "banana", "kiwi", "orange"]
sorted_words = sorted(words, key=lambda x: len(x))
print(sorted_words)  # 输出: ['kiwi', 'apple', 'orange', 'banana']
```

### 列表推导式

列表推导式 (List Comprehension)，用一行代码从一个现有的可迭代对象（如列表、元组、字符串、range 对象等）中生成一个新的列表，并可以对元素进行过滤和转换。

**语法**
```
 new_list = [expression for item in iterable if condition]
```
 
```
 expression: 对 item 进行操作的表达式，结果将成为新列表中的元素
 item: 可迭代对象中的每个元素
 iterable: 一个可迭代对象（列表、元组、字符串、range、集合等）
 if condition: 可选的条件语句。只有当 condition 为 True 时，item 才会被处理并添加到新列表中
```

**执行过程**
1. 迭代： 列表推导式会遍历 iterable 中的每个 item
1. 条件判断（可选）： 如果提供了 if condition，则检查 condition 是否为 True
1. 表达式计算： 如果 condition 为 True（或者没有 if condition），则计算 expression 的值
1. 添加到列表： 将 expression 的结果添加到 new_list 中
1. 重复步骤 1-4，直到 iterable 中的所有元素都被处理完毕

**Sample**

0-9 的平方:
```
 squares = [x**2 for x in range(10)]
```

将字符串列表中的所有单词转换为大写：
```
 words = ["cat", "dog", "elephant", "bird", "ant"]
 upper_words = [word.upper() for word in words]
```

过滤出长度大于 3 的单词:
```
 long_words = [word for word in words if len(word) > 3]
```

嵌套列表推导式:
```
 matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
 flattened = [num for row in matrix for num in row]
```

三元表达式:
```
 numbers = [1, 2, 3, 4, 5]
 results = ["even" if x % 2 == 0 else "odd" for x in numbers]
```

使用列表推导式，是为了简洁，逻辑可以用一行清晰地表达。如果转换或过滤逻辑较为复杂，使用 for 更清晰易懂，比如上面例子中，后两个就稍复杂了已经。

### 动态执行
1. eval() 函数用于计算一个 Python 表达式，并取得返回值。
```
 eval(expression)
```
```
 exp = 'lambda x, y: x + y'
 add = eval(exp)
 result = add(5, 3)
```
2. exec() 函数用于执行一段 Python 代码，返回值 None。
```
 exec(Python Code)
```
```
 code = "print('Hello from exec!')"
 exec(code)  # 执行代码
```

极度危险的例子
```
 code = input("Enter some Python code: ")
 exec(code)
 # 用户可以输入 "import os; os.system('ls -l /')" 查看系统信息，恶意用户甚至可以删除整个文件系统！
```

### 解包操作符

在 Python 中，* 有多种用途，其中之一就是**解包**操作符。
 ```
list1 = [1, 2, 3]
tuple1 = (4, 5, 6)
list2  = [*list1, *tuple1]
tuple2 = (*list1, *tuple1)
tuple3 = (*list1,)

# Python 不允许你把一个元组（隐式的 (1, 2, 3)）赋值给一个变量，然后又期望这个变量变成一个元组。 
  
这在语法上是不明确的，也是没有必要的。
如：与函数参数解包的混淆：my_func(*list1)
fun1(*list1)      # -> fun1(1,2,3)
```
