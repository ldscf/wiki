---
source_title: C++ Template
categories:
- C++
- Develop
last_modified: '2024-12-25T01:19:13Z'
---
模板(template)是泛型编程的基础，泛型编程即以一种独立于任何特定类型的方式编写代码。

模板是创建泛型类或函数的蓝图或公式。库容器，比如迭代器和算法，都是泛型编程的例子，它们都使用了模板的概念。

每个容器都有一个单一的定义，比如向量，我们可以定义许多不同类型的向量，比如 vector  或 vector 。

可以使用模板来定义函数和类。

### 模板（Template）

　　指C++程序设计设计语言中采用类型作为参数的程序设计，支持通用程序设计。
- typename
- class

　　对于基础类型，如：int, char等来说，两者实际上可以说没有区别。按 C++ 标准来说，template 用于基础数据类型，typename 指类型名，T 可以取 char int double 等。template 用于类，T 可以取任何类。

　　特例：当 T 是一个类，而这个类又有子类(假设名为 innerClass) 时，应该用 template。如定义：typename T::innerClass myInnerObject; 这里的 typename 告诉编译器，T::innerClass 是一个类，程序要声明一个 T::innerClass 类的对象，而不是声明 T 的静态成员，而 typename 如果换成 class 则语法错误。

### Example:

#### 计算 int 数组的长度
 ```
int array_len(const int& arr) {
  return sizeof(arr)/sizeof(arr[0]);
}
```

#### 计算任意数据类型数组的长度
 ```
template 
int array_len(const T& arr) {
  return sizeof(arr)/sizeof(arr[0]);
}
```
