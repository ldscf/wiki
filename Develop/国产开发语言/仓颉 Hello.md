---
source_title: 仓颉 Hello
categories:
- Develop
- 国产开发语言
last_modified: '2024-03-18T08:11:03Z'
---
### Hello
```
 // Hello, World!
 func fun1(str1:String) {
    println("Output " + str1 + " from fun1.\n")
 }
```
 
```
 main() {
    var s1 = "Hello, World!"
    println(s1)
    fun1("test")
 }
```
```
 # compile
 cjc hello.cj -o hello
 # hello 793664(VSCode: 795384)
```
```
 # result
 Hello, World!
 Output test from fun1.
```

### C function
- 将调用的函数在 foreign 中定义，基本变量类型改为仓颉类型，变长用 ... 表达
- 结构体定义 @C
- 使用时，用 unsafe{}

编译时，会使用 C 库，如：gcc lib path: /lib/gcc/x86_64-linux-gnu/9。用 cjc ... -V 查看。
```
 *foreign {
     func srand(n: Int32): Unit
     func time(): Int32
     func rand(): Int32
 }
```

 
```
 main() {
    unsafe {
       var n1:Int32 = time()
       srand(n1)
    }
```

 
```
    let r1 = unsafe {
       rand()
    }
    println(r1)
 }*
```

### 封装 C function
```
 *// foreign 中不允许重复引用函数
 // 建议每个引用函数单独一行
 foreign {func srand(n: Int32): Unit}
 foreign {func time(): Int32}
 foreign {func rand(): Int32}
```

 
```
 public func urand(): UInt32 {
     unsafe {
         var n1:Int32 = time()
         srand(n1)
     }
```

 
```
     let r1 = unsafe {
         rand()
     }
     UInt32(r1)
 }
```


```
 main() {
     var n1 = urand()
     println(n1)
 }*
```

### 封装 C function struct

调用方式类似类方法
```
 *foreign {func srand(n: Int32): Unit}
 foreign {func time(): Int32}
 foreign {func rand(): Int32}
 public struct UDEF {
     //-- rand    
     public func urand(): UInt32 {
         unsafe {
             var n1:Int32 = time()
             srand(n1)
         }
```

     
```
         let r1 = unsafe {
             rand()
         }
         UInt32(r1)
     }
     //== rand
 }
```


```
 main() {
     var udef1 = UDEF()
     var n1 = udef1.urand()
     println(n1)
 }*
```

### 打包
```
 cjpm init UDEF usys --type=static
 cjpm build
```
1. 第一步会生成 module.json 文件，生成 src 目录
1. 在第二步前，将 usys.cj 放入 src
1. 第二步执行后，在 release 中生成 usys 目录及相应 .cjo 文件
```
 {
   "cjc_version": "0.45.2",
   "organization": "UDEF",
   "name": "usys",
   "description": "nothing here",
   "version": "1.0.0",
   "build_dir": "",
   "src_dir": "",
   "dependencies": {},
   "requires": {},
   "dev_requires": {},
   "package_requires": {
     "path_option": [],
     "package_option": {}
   },
   "foreign_requires": {},
   "output_type": "static",
   "command_option": "",
   "condition_option": {},
   "link_option": "",
   "cross_compile_configuration": {},
   "scripts": {},
   "package_configuration": {},
   "lto": ""
 }
```
```
 src
 usys.cj
```
```
 build/release/usys/UDEF.cjo
 build/release/usys/libusys_UDEF.a
 build/release/usys/incremental-cache.json
```
