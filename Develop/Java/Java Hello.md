---
source_title: Java Hello
categories:
- Develop
- Java
last_modified: '2024-02-01T02:42:15Z'
---
First Java program, test Java class, encapsulation, Inheritance.

*JavaHello.java*
- *udf/IUdfHello.java*
- *udf/UdfHello.java*
- *udf/IUdfHelloJ.java*
- *udf/UdfHelloJ.java*

编译
```
 javac JavaHello.java
```

执行
```
 java JavaHello
```

#### JavaHello.java
```
 import udf.UdfHello;
```
 
```
 public class JavaHello { 
     public static void main(String args[]) {
```
 
```
         System.out.println("==== Base Class ====");
         UdfHello test1 = new UdfHello("Hello, World!", 0.1);
         test1.info();
         System.out.println(test1.getInfo());
         System.out.println(test1.VERSION);
```
 
```
         System.out.println("==== Inheritance ====");
         UdfHelloJ test2 = new UdfHelloJ();
         test2.info();
         System.out.println(test2.getInfo());
         System.out.println(test2.VERSION);
```
 
```
         System.out.println("==== Encapsulation ====");
         UdfHelloJ test3 = new UdfHelloJ();
         test3.setVer(2.1);
         test3.setInfo("This is a test setInfo.");
         System.out.println(test3.getVer());
         System.out.println(test3.getInfo());
```
 
```
         System.out.println("==== Polymorphism ====");
         UdfHello test4 = new UdfHelloJ();
         test4.info();
         System.out.println(test4.getInfo());
         System.out.println(test4.VERSION);
     }
 }
```

#### IUdfHello.java
```
 package udf;
```
 
```
 public interface IUdfHello {
     public void setVer(double ver);
     public void setInfo(String info);
     public double getVer();
     public String getInfo();
     public void info();
 }
```

#### UdfHello.java
```
 package udf;
```
 
```
 /*
 ------------------------------------------------------------------------------
   Name     : UdfHello
   Purpose  : Java Sample
   Author   : Adam
   Revisions:
   Ver        Date        Author           Description
   ---------  ----------  ---------------  ------------------------------------
   1.0        2024/1/22   Adam             My first Java program.
   1.1        2024/1/22   Adam             M01: comments & Spaces/CR/LF of code do not affect the compilation result
```
 
```
  format:
     object  : (), (string, double)
     property: VERSION
     method  : info
 ------------------------------------------------------------------------------
 */
 public class UdfHello implements IUdfHello {
     // private static final String Version = "v1.0";
     public static final String VERSION = "v1.0";
     private double Version = 1.0;
     private String INFO;
```
 
```
     // constructor with no parameter
     public UdfHello() {
         INFO = "Hello, World!";
     }
```
 
```
     // constructor & parameter
     public UdfHello(String info, double ver) {
         System.out.println("Variable: info=" + info + ", ver=" + ver);
         try {
             INFO = info;
             Version = ver > Version ? ver : Version;
             // Error: error: cannot assign a value to final variable VERSION  // M01:
             //  VERSION = "v1.1";
         }
         catch(Exception e) {
             System.out.println(e);
         }
```
 
```
     }
```
 
```
     public void info() {
         System.out.println("VERSION: " + VERSION);
         System.out.println("Ver: v"    + Version);      // M01:
         System.out.println(INFO);
         System.out.println("");
     }
```
 
```
     public void setVer(double ver) {
         Version = ver > Version ? ver : Version;
     }
     public void setInfo(String info) {
         INFO = info;
     }
     public double getVer() {
         return Version;
     }
     public String getInfo() {
         return INFO;
     }
 }
```

#### IUdfHelloJ.java
```
 package udf;
```
 
```
 public interface IUdfHelloJ extends IUdfHello {
     public void setVer(double ver);
 }
```

#### UdfHelloJ.java
```
 package udf;
```
 
```
 import udf.UdfHello;
```
 
```
 public class UdfHelloJ extends UdfHello implements IUdfHelloJ {
     public static final String VERSION = "v1.1";
```
 
```
     // constructor with no parameter
     public UdfHelloJ() {
         super.setInfo("Hello, Java!");
     }
```
 
```
     public String getInfo() {
         return super.getInfo() + "from UdfHelloJ.";
     }
 }
```

#### Result
```
 ==== Base Class ====
 Variable: info=Hello, World!, ver=0.1
 VERSION: v1.0
 Ver: v1.0
 Hello, World!
 Hello, World!
 v1.0
 ==== Inheritance ====
 VERSION: v1.0
 Ver: v1.0
 Hello, Java!
 Hello, Java!from UdfHelloJ.
 v1.1
 ==== Encapsulation ====
 2.1
 This is a test setInfo.from UdfHelloJ.
 ==== Polymorphism ====
 VERSION: v1.0
 Ver: v1.0
 Hello, Java!
 Hello, Java!from UdfHelloJ.
 v1.0
```
