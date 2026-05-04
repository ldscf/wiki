---
source_title: Python 闭包
categories:
- Develop
- Python
last_modified: '2025-08-05T03:12:54Z'
---
在一些语言中，在函数中可以（嵌套）定义另一个函数时，如果内部的函数引用了外部的函数的变量，则可能产生闭包（closure）。闭包可以用来在一个函数与一组“私有”变量之间创建关联关系。在给定函数被多次调用的过程中，这些私有变量能够保持其持久性。(From Wikipedia)

### Python 闭包

在一个内部函数中，对外部作用域的变量进行引用，(并且一般外部函数的返回值为内部函数)，那么内部函数就被认为是闭包。
- 必须有一个嵌套函数(函数内部的函数)
- 嵌套函数必须引用封闭函数中定义的值
- 闭包函数必须返回嵌套函数

##### 一、基本形态
```
 def outer(x):
      def inner(y):
          nonlocal x
          x+=y
          return x
      return inner
```
 
```
 a = outer(10)
 a(1)
 # 相当于 print(outer(10)(1))
```

##### 二、扩展原函数功能
```
 def deco1(fun1):
     def wrapper():
         print("decorator begin...")
         fun1()
         print("decorator end...")
     return wrapper
```
 
```
 @deco1
 def fplus():
     print("fplus")
```
 
```
 fplus()
```

#### 带返回值及参数
```
 def deco1(fun1):
     def wrapper(*args, **kwargs):
         print("decorator begin...")
         res = fun1(*args, **kwargs)
         print("decorator end...")
         return res
     return wrapper
```
 
```
 @deco1
 def fplus(x = 0,y = 0):
     print("fplus")
     return x+y
```
 
```
 v1 = fplus(1,2)
 print(v1)
```

#### 带返回值及参数 II
```
 def fplus(x = 0,y = 0):
     print("fplus")
     return x+y
```
 
```
 def deco1(fun1):
     def wrapper(*args, **kwargs):
         print("decorator begin...")
         res = fun1(*args, **kwargs)
         print("decorator end...")
         return res
     return wrapper
```
 
```
 @deco1
 def f(*args, **kwargs):
      return fplus(*args, **kwargs)
```
 
 
```
 v1 = f(1,2)
 print(v1)
```
