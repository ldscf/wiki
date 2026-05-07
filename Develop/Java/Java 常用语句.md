---
source_title: Java 常用语句
categories:
- Develop
- Java
last_modified: '2025-05-15T02:03:19Z'
---
### 表达式

#### 逻辑判断
- ||(|): 逻辑或。前者当得出结果时，不会继续执行后面的表达式。如：(x <= 10 || i++ > 10)
- &&(&): 逻辑与。同上。

#### 可变参数
```
 fun (String... p1)
 ...
 p1.length > 1
 p1[0]
```

#### 三元
```
 int i = a > b ? 1 : 0;
```

### 初始化

#### List
```
 # 不可变
 List l1 = Arrays.asList("ISO8601", "YAML", "YML");
```
 
```
 # 不可变, Java 9
 List l1 = List.of("ISO8601", "YAML", "YML");
```
 
```
 # 可变
 List l2 = new ArrayList<>(Arrays.asList("ISO8601", "YAML"));
 l2.add("YML");
```

#### Array
```
 int[] na1 = {1,2,3,4,5};
 na1[0] = 0;
```

#### Map
```
 # 不可变 map, Java 9 方法，支持最多 10 对键值对
 Map.of(key1, val1, key2, val2);
```

### 转换

#### 数值 & 字符串
```
 // int -> string
 String.valueOf(i)    // 产生一个对象
 // 其它数值类型、字符与字符数组(可以指定局部)同样转换
 "" + i               // 产生两个 String 对象
```
```
 // int -> string
 String s = "10";
 Integer.parseInt(s)
```

#### byte & 字符串
```
 // byte-> string
 String s1 = new String(b1);
```
```
 // string -> byte
 byte[] b1 = s1.getBytes();
```

#### 数组转为字符串
```
 int[] a1 = {1,2,3,4,5};
 Arrays.toString(a1);
```

#### 字符串转换字符
```
 s.charAt(i)
```

#### List & Set
```
 List list1 = new ArrayList<>(set1)
 Set set1 = new HashSet<>(list1)
```

#### List & Array
```
 String[] sarray1 = list1.toArray(new String[list1.size()]);
 // 数据转为 List，如果直接使用 Arrays.asList，会创建一个不可变的 List。可以使用 ArrayList 的构造器创建一个可变的 List。
 List list1 = new ArrayList<>(Arrays.asList(sarray1));
```

#### Object & String
```
 // Map: Object --> String
 Map m1 = new HashMap();
 Map m2 = (Map) m1;
```
 
```
 // Object --> Map(String, Object)
 // 通过 gson 转换，val 是一个通过 snakeyaml 读出来的结果
 //import com.google.gson.Gson;
 //import com.google.gson.reflect.TypeToken;
 Gson json = new Gson();
 json.fromJson(json.toJson(val.get(key)), new TypeToken>(){}.getType())
 // 下面的转换方式，当 key 为数值时，Map 中 key 也为数值
 (Map)val.get(key)
```

#### Object 判断
```
 *public static boolean isnull(Object obj1) {        
```

     if      (obj1 instanceof String)     { return ((String)     obj1).isEmpty(); }

     else if (obj1 instanceof List)       { return ((List)       obj1).isEmpty(); }

     else if (obj1 instanceof Map)        { return ((Map)        obj1).isEmpty(); }

     else if (obj1 instanceof Properties) { return ((Properties) obj1).isEmpty(); }

     else                                 { return false; }
```
 }*
```
```
 *public static boolean isnull(Object obj1) {
```

     String type1 = obj1.getClass().toString();

     type1 = type1.substring(type1.lastIndexOf(".") + 1);

     switch (type1) {

         case "String"     : return ((String)       obj1).isEmpty();

         case "ArrayList"  : return ((ArrayList<?>) obj1).isEmpty();

         case "HashMap"    : return ((HashMap<?, ?>)obj1).isEmpty();

         case "Properties1": return ((Properties)   obj1).isEmpty();

     }

     return false;
```
 }*
```

#### json 转换
 ```

# fastjson
    com.alibaba
    fastjson
    1.2.28
List l1 = new ArrayList<>();
Map m1 = new LinkedHashMap<>();
m1.put("user", "Adam");
m1.put("val", 3);
l1.add(new JSONObject(m1));
 ## 以下是用 gson 转换，但 gson.toJson(val) 是将 val(Map)转换成了 String，输出没问题，但是通过 spring-boot List返回就会出现 \" 现象。
 <!--gson-->
 
     com.google.code.gson
     gson
     2.8.9
 
 
 import com.google.gson.Gson;
 Gson gson = new Gson();
 String json1 = gson.toJson(val);
 
 Map> val1 = new HashMap<>();
 val1 = gson.fromJson(json1, HashMap.class);
 
 # 可以验证 val 的合理性，正确的话 val2 = val
 Map val = ...;
 Map val2 = json1.fromJson(gson.toJson(val), new TypeToken>(){}.getType());
 
 - and -
 HashMap> val;
 val = new HashMap>();
 
 -.OR.-
 
 <!-- Json -->
 
     com.googlecode.json-simple
     json-simple
     1.1.1
 
 
 import org.json.simple.JSONObject;
 JSONObject jval;
 jval = new JSONObject();
 jval.toJSONString(val)
```

### 条件

#### 判断一个字符串为空
```
 null == s || "".equals(s)
```

#### 比较两个字符串，true=相等
```
 s.equals(s2)
```

#### 找出字符串位置，-1=不含
```
 s.indexOf(s2)
```

#### 判断 key 是否存在
```
 map1.containsKey(key1)
```

#### 获取变量类型

.getClass().toString()

### 循环

#### list
```
 List l1;
 for (String s1 : l1) {
```

     System.out.println(s1);
```
 }
```

#### map
```
 // Map m1 = new LinkedHashMap<>(Map.of("a", "1"));
 // 用 m1 中的 key-value 替换 s1 中的相应的 %key% 变量
 for (String key : m1.keySet()) {
```

     s1 = s1.replaceAll(String.format("%c%s%c", '%', key, '%'), m1.get(key));
```
 }
 *// 下面是一种不是很好的 lambda 写法
 // Error: 从lambda 表达式引用的本地变量必须是最终变量或实际上的最终变量
 m1.forEach((key, value) -> {
```

     System.out.println(String.format("key: %s, val: %s", key, value));
```
 //   s1 = s1.replaceAll(String.format("%c%s%c", '%', key, '%'), value);
 });*
```

#### properties
```
 // Properties tmp = new Properties();
 for (String key : tmp.stringPropertyNames()) {
```

     System.out.println(key + "=" + tmp.getProperty(key));
```
 }
```

#### Arrry
```
 // Map m1 = new HashMap<>();
 // String s1 = "a,b,c";
 // String[] a1 = s1.split(",");
 for(int i=0; i
 2024-03-20 09:25:42,222 INFO [test] test logger
```

一般建议使用: Thread.currentThread().getStackTrace()[3].getFileName()

#### 执行操作系统命令

一般使用 Runtime.getRuntime().exec(CMD) 执行 CMD 命令，并用 BufferedInputStream/BufferedReader 接收返回结果。但 exec 直接执行命令却无法处理管道符号，如：ps -ef|grep abc。需要通过 OS shell 解释命令：
```
 cmdArray = new String[]{"/bin/sh", "-c", CMD};
 Runtime.getRuntime().exec(cmdArray)
```

### 常见问题

#### 同一个对象多次 add

添加对象时，添加的是它的引用。所以多次 add，发现多条记录都是最后对象的值。

每次 new 对象再 add，如：
```
 Map> val = new HashMap<>();
 List l1 = new ArrayList<>();
 for (i=0;...)
```

     l1 = new ArrayList<>();

     ...

     val.put(i, l1);

#### HashMap 顺序

如果需要严格按照 put 顺序，可以使用 LinkedHashMap。
