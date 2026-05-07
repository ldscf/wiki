---
source_title: 仓颉 Error
categories:
- Develop
- 国产开发语言
last_modified: '2024-03-13T09:22:05Z'
---
以下错误出现在仓颉 0.45.2 cjnative for Linux 版本。

__TOC__

#### finalizer is forbidden in class 'XXX' that is open
```
 *open 类不允许有析构函数*
```

#### OverflowException: typecast

random 中出现，下列代码有很大机率出现错误。
```
 
```

目前的解决办法是多试几次 nextInt64()。
```
 <// rand, random
 public func rand(l : UInt64) : UInt64 {
```

     let r: Random = Random()

     r.seed = dt()

     var i : UInt64 = 0

     var flag : Bool = true

     while (flag) {

         try {

             i = UInt64(r.nextInt64())

             flag = false

         } catch (e: Exception) {

             println(e)

             i = 0

         }

     }
     
     UInt64(i % UInt64(10 ** l))
```
 }
```

#### cjpm: error while loading shared libraries: libcrypto.so.3

error while loading shared libraries: libcrypto.so.3: cannot open shared object file: No such file or directory

需要 openssl 3，安装参见 [[openssl]]
