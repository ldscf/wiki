---
source_title: Java 泛型
categories:
- Develop
- Java
last_modified: '2024-05-31T02:38:05Z'
---
Java 泛型

泛型(generics)，即“参数化类型”，用于编写更通用、类型安全的代码。定义时由参数代替具体的类型，使用时传入具体的类型(即形参不指定类型，由实参指定)。

### 泛型类型

#### 泛型类
```
 public class Box {
     private T content;
     ...
 }
```
 
```
 Box intBox = new Box<>(123);
 Box stringBox = new Box<>("Hello, World!");
```

#### 泛型方法
```
 private Node root;
 public , V> void insert(K key, V value) {
     root = insert(root, key, value);
     ...
 }
```

#### 泛型接口
```
 interface Generator {
     T generate();
 }
```
 
```
 class StringGenerator implements Generator {
     @Override
     public String generate() {
         return "Generated String";
     }
 }
```

### 泛型参数

#### 

Comparable 是指类型 T 必须实现 Comparable 接口，即 T 之间能比较大小(Java 不支持运算符重载，只能通过 Comparable 接口比较两个对象)

#### >

实现 Comparable 接口；输入参数可以是  或其子孙类。

### 判断泛型类型
```
 public static  T t1(Object t1) {
     log(type(t1));
     return (T)t1;
 }
 public static  A t2(A t1) {
     log(type(t1));
     return t1;
 }
```
 
```
 List l1 = new ArrayList<>();
 Map m1 = new HashMap<>();
 m1.put("a","1");
 log(t1(l1));
 log(t1(m1));
```

### java 伪泛型

Java 和 C++ 都提供了泛型功能，但两者在实现和功能上存在一些关键差异。由于 Java 泛型在某些方面存在限制，因此有时被称为“伪泛型”。
- 擦除类型信息

Java 和 C++ 的一个主要区别在于泛型类型信息的处理方式。在 Java 中，泛型类型参数会在编译过程中被擦除，即替换为其原始类型。例如，List 会被擦除为 List。这意味着 Java 泛型在运行时并不知道实际使用的具体类型是什么，这限制了一些泛型功能的实现，例如泛型方法和泛型继承。

C++ 则不会擦除泛型类型信息。这意味着 C++ 泛型在运行时仍然知道实际使用的具体类型，这使得 C++ 泛型可以实现 Java 泛型无法实现的功能，例如泛型方法和泛型继承。
- 泛型实例化

Java 和 C++ 对泛型实例化的支持也存在差异。Java 泛型只能实例化为引用类型，不能实例化为基本类型。这意味着不能创建像 List 这样的泛型实例。C++ 则支持泛型实例化为引用类型和基本类型。
- 类型安全

由于 Java 泛型在运行时并不知道实际使用的具体类型是什么，因此存在潜在的类型安全问题。例如，如果向 List 中添加一个 String 对象，则会导致运行时错误。C++ 泛型由于保留了类型信息，因此在类型安全方面具有更好的优势。
