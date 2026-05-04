---
source_title: C++ 常用语句
categories:
- C++
- Develop
last_modified: '2025-06-11T08:16:33Z'
---
### 赋值

#### map
 ```
unordered_map um1 = {{"apple", 1}, {"banana", 2}, {"orange", 3}};
// map 相同
```

#### 结构体
 ```
struct GVAL {
    int t;    // 0=NULL, 1=int, 2=string
    int i;
    string s;
};
GVAL res1;
res0 = {0};
res1 = {1, 10};
res2 = {2, 0, "ok"};
-.OR.-
res2.t = 2;
res2.s = "ok";
// gcc version 9.4.0
```

#### 数组
 ```
char buffer[msg_len + 1] = {0};          // 全部为 \0
memset(buffer, '\0', sizeof(buffer));    // 全部为 \0
P.S. char buffer[msg_len + 1] = {"a"}    // "a", \0, \0...
```

### 转换

#### 数值 <-> 字符串
```
 // 数值 -> 字符串
 #include 
 str1 = to_string(num1);
```
 
```
 // 字符串 -> 数值
 #include 
 num1 = stoi(str1);
```

#### 字符数组 <-> 字符串
```
 // 字符数组 -> 字符串
 string str1(ch1);
 // .-OR-.
 str1 = ch1;
```
 
```
 // 字符串 -> 字符数组
 len1 = str1.copy(ch1, str1.size());
 ch1[len1] = '\0';
 // .-OR-.
 strcpy(ch1, str1.c_str());
 //strncpy(ch1, str1.c_str(), 10);
```

### 条件

#### map 元素是否存在
```
 map map1;
 map1["key1"] = 123;
```
 
```
 if (map1.count("key1")) {
     ...
 }
```

#### try
```
 try {
     return stoi(str1);
 } catch (exception e) {
     return 0;
 }
```

#### 三元运算符
```
 condition ? true_expression : false_expression;
 int max = (a > b) ? a : b;
```

### 循环

#### map
```
 # C++11
```

for (const auto& pair1 : map1) {
```
    cout << pair1.first << ": " << pair1.second << endl;
```

}

# 基本方法：迭代器

for (map<int, string>::iterator it = map1.begin(); it != map1.end(); ++it) {
```
    cout << it->first << ": " << it->second << endl;
```

}

# 函数封装

#include <algorithm>

void printPair(const pair<int, string>& p) {
```
    cout << p.first << ": " << p.second << endl;
```

}

...

for_each(map1.begin(), map1.end(), printPair);

### chrono

#### 延时函数
```
 #include             // sleep_for
 #include             // milliseconds
 std::this_thread::sleep_for(std::chrono::milliseconds(200));  // Threads sleep for 200 ms
```

usleep 问题
* 在一些平台下不是线程安全，如 HP-UX 以及 Linux
* usleep 影响信号

### Compile

#### 当前函数名

在大多数编译器中，__FUNCTION__ 宏会被替换为当前函数名
```
 cout << "Current Function: " << __FUNCTION__ << endl;
```
* __FUNCTION__        : myfun
* __func__            : myfun
* __PRETTY_FUNCTION__ : void sys_info(string), gcc
