---
source_title: 仓颉 BASE
categories:
- Develop
- 国产开发语言
last_modified: '2024-03-14T05:21:49Z'
---
提供日期、随机数字、字符串操作等基础函数。
```
 *<small>from std import time.*
 from std import random.*
```

 
```
 open class BASE {
```

     let VERSION : String = "v1.0"

     static let tb : Int64
 
     static init() {

         tb = 1234567890

     }
 
     public func getVer() : String {

         tb

         VERSION

     }
 
     // datetime, time

     public func dt() : UInt64 {

         _dt().toString()

         _dt()

     }

     public func dt(i : UInt64) : UInt64 {

         UInt64(_dt()) / UInt64(10 ** (14-i))

     }

     public func ms() : UInt64 {

         UInt64(_nsec()) / UInt64(10 ** 6)

     }

     protected func _dt() : UInt64 {

         var now = DateTime.now()

         let d : Int64 = (now.year*100 + now.monthValue)*100 + now.dayOfMonth

         let t = (now.hour*100 + now.minute)*100 + now.second

         UInt64((d*1000000 + t))

     }

     protected func _nsec() : UInt64 {

         var now = DateTime.now()

         let ms : Int64 = now.nanosecond

         UInt64(ms)

     }
 
     // rand, random

     public func rand() {

         _rand()

     }

     public func rand(l : UInt64) {

         //UInt64(_rand() % UInt64(10 ** l))

         UInt64(_rand() / UInt64(10 ** (19 - l)))

     }

     protected func _rand() : UInt64 {

         let r: Random = Random()

         r.seed = _nsec()

         var i : UInt64 = 0

         var flag : Bool = true

         while (flag) {

             try {

                 i = UInt64(r.nextInt64())

                 if (i >= UInt64(10 ** 18)) {

                     flag = false

                 } else {

                     //println("number too small.\n")

                 }

             } catch (e: Exception) {

                 //println(e)

             }

         }

         UInt64(i)

     }
```
 }
```

 
```
 <i>main() {
```

     var n1 :Int64 = BASE.tb

     println("Class Property: " + BASE.tb.toString())
  
     let b1 = BASE();

     println("Method  : " + b1.getVer())

     println("Property: " + b1.VERSION)
 
     println("DateTime: " + b1.dt().toString())
 
     print("random number: ")

     println(b1.rand())
```
 }</i>*
```
