---
source_title: Equals 方法
categories:
- Develop
- Java
last_modified: '2024-11-04T02:21:24Z'
---
在 String 比较中使用 equals 方法，可以比较两个 String 的值是否相等，而这个方法是 String 重写的（ArrayList 等也进行了重写）。

而在 Object 中使用原生的方法，比较的只是两个对象是否相等，所以创建两个空的 Object，比较结果是不相等的。

而类似的情况是，== 可用于比较基本类型相等。而对于非基本类型，== 比较的是地址。

下面是一个自定义类的例子，展示两个对象内容相同但 equals 返回 false 的情况：
 ```
class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
    // use the default equals of Object
}
public class test_equals {
    public static void main(String[] args) {        
        System.out.println("==== Person compare:");
        Person p1 = new Person("Adam", 20);
        Person p2 = new Person("Adam", 20);
        System.out.println(p1);
        System.out.println(p2);
        System.out.println(p1.equals(p2));  // false
    }
}
```

在自定义类中通常需要重写 equals 方法。
