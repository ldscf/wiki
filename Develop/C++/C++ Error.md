---
source_title: C++ Error
categories:
- C++
- Develop
last_modified: '2024-12-25T01:18:32Z'
---
### runtime

#### stoi
```
 // 在字符串数值转换过程中，如果输入了非数值如：- 报错
 terminate called after throwing an instance of 'std::invalid_argument'
```

  what():  stoi
```
 Aborted (core dumped)
```

#### string.substr
```
 # string 截取长度超过了字符串长度
 terminate called after throwing an instance of 'std::out_of_range'
```

   what():  basic_string::substr: __pos (which is 1) > this->size() (which is 0)
```
 Aborted (core dumped)
```

#### Unknown memory
```
 struct GREAD {
```

int t;

queue<string> s;

};

map<int, GREAD> MSG;

...

int fd;

MSG[!fd].s.empty() // Unknown memory

!MSG[fd].s.empty() // Correct operation

### compile

#### undefined reference to pthread_create
```
 -pthread
```

#### 在构造函数内初始化引用成员变量

**（错误说明）引用成员变量必须在构造函数的初始化列表（Constructor Initialization List）中进行初始化。这是因为引用一旦被初始化，就不能改变其引用的对象。因此，它必须在声明时就被初始化。(来自[https://developer.aliyun.com/article/1467283 《C++深度解析：引用成员变量的初始化及其在模板编程中的应用（一）》])**

根据下面两段说明，**确切的说法**是：引用成员变量之所以必须在构造函数的初始化列表中进行初始化，是因为引用在声明时必须初始化为一个已存在的对象。所以，在构造函数内初始化引用成员变量，报错的是声明时未初始化(int& myRef)。

*引用（reference）是 C++ 中的一个别名，它在声明时必须初始化为一个已存在的对象。一旦初始化，引用就和它所引用的对象绑定在一起，它们共享同一个内存地址

*引用本质上是一个常量指针，它只是给一个已存在的对象起了另外一个名字。这个名字一旦确定，就不能再指向其他的对象
 ```
class MyClass {
 public:
     MyClass(int& ref) {
         myRef = ref;  // Error
     }
 private:
     int& myRef;
 };
 
 class MyClass {
 public:
     MyClass(int& ref) : myRef(ref) {}  // OK
 private:
     int& myRef;
 };
```
