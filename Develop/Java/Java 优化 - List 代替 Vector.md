---
source_title: Java 优化 - List 代替 Vector
categories:
- Develop
- Java
last_modified: '2025-07-29T06:22:40Z'
---
如果不考虑到线程的安全因素，一般用 Arraylist 效率比较高。
- Vector 是线程同步的（所有公共方法都使用了 synchronized 关键字进行同步），是线程安全的，而 Arraylist 没有内置同步机制^[1]^
- Vector 在长度不够用时在原来的基础上扩展 100%，ArrayList 扩展 50%
```
 private static Vector splitString(String src) {
```

     Vector spliter = new Vector<>();

     if (src == null) {

         return spliter;

     }

     ...
```
 }
```
1. 使用 ArrayList 代替 Vector，提供了更好的性能，Vector 是较旧且效率较低的方法
1. 空值返回采用更稳妥的 Collections.emptyList 方式^[2]^

修改后，代码效率提升 20% 以上。
```
 private static List splitString(String src) {
```

     if (src==null||(src.equals(""))) {

         return Collections.emptyList();

     }

     List spliter = new ArrayList<>();

     ...
```
 }
```

### 参考
1. 在现代 Java 并发编程中，有更高效和灵活的线程安全集合（如 Collections.synchronizedList 或 CopyOnWriteArrayList）以及并发控制机制（如 java.util.concurrent 包），所以 Vector 已很少使用
1. Collections.emptyList()返回的是一个不可变且单例的空列表，不涉及对象的创建和垃圾回收，比返回 new ArrayList<>() 更高效
