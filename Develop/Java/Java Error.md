---
source_title: Java Error
categories:
- Develop
- Java
last_modified: '2024-11-07T05:37:53Z'
---
### 版本

#### toArray

toArray() 方法将 List、Set 等集合转换为数组。

List l1...
- l1.toArray(new String[0]) : 适合于 Java 1.2 及以上版本
- l1.toArray(String[]::new) : 适合于 Java 8 及以上版本

### 方法特殊性

#### replaceAll

在 replaceAll 方法中使用 Pattern.quote() 来处理特殊字符，这样可以确保输入分隔符（如 $）不会被解释为正则表达式中的特殊字符。
```
 src = src.replaceAll(Pattern.quote(key), val);
```

### 无法从静态上下文中引用非静态方法

静态(static)方法和静态变量属于某一个类，而不属于类的对象。static 在类加载的时候就会分配内存，可以通过类名直接去访问。非静态成员属于类的对象，在对象初始化之后存在。

在静态方法中调用非静态成员，相当于调用了一个还未初始化的变量。

### snakeyaml

#### 非法字符

yaml 文件中值包含 %，一般情况下，此条目会不读
```
 while scanning for the next token
 found character '%' that cannot start any token. (Do not use % for indentation)
```

#### 版本
```
 java.lang.NoClassDefFoundError: org/yaml/snakeyaml/inspector/TagInspector 
```

在 Java 程式中無法找到 TagInspector 類別的錯誤。這通常意味著 SnakeYAML 套件的相依性版本不正確或是丟失了所需的類別。TagInspector 是在較新版本的 SnakeYAML 中新增的類別，因此可能是因為使用了較舊的版本而導致找不到這個類別。TagInspector 在 2.0 或以上版本。

### 路径中存在多个 SLF4J 绑定
```
 SLF4J: Class path contains multiple SLF4J providers.
 SLF4J: Found provider [ch.qos.logback.classic.spi.LogbackServiceProvider@4883b407]
 SLF4J: Found provider [org.slf4j.reload4j.Reload4jServiceProvider@7d9d1a19]
 SLF4J: See https://www.slf4j.org/codes.html#multiple_bindings for an explanation.
 SLF4J: Actual provider is of type [ch.qos.logback.classic.spi.LogbackServiceProvider@4883b407]
```

通常，项目可能会包含以下几种依赖：
- SLF4J API: 这是 SLF4J 的 API，用于定义日志记录的接口。它不包含任何实现代码，因此不会产生冲突
- SLF4J Binding: 这是一个 SLF4J 实现，如 Logback 或 Log4j。每个项目通常只需要一个
- Logging Implementation: 这是具体的日志记录实现库，如 logback-core 或 log4j-core

一般来说，spring Boot 中 包含 slf4j，所以如果另外引用了 slf4j 就会出现 multiple SLF4J providers.
1. 找到冲突包所在位置
```
 mvn dependency:tree
 # MacOS 下 mvn 路径可能在 /Applications/IntelliJ\ IDEA\ CE.app/Contents/plugins/maven/lib/maven3/bin/
```
 
 ```
[INFO] com.example:testSprintBoot:jar:0.0.1-SNAPSHOT
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:3.3.2:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter:jar:3.3.2:compile
[INFO] |  |  +- org.springframework.boot:spring-boot:jar:3.3.2:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-autoconfigure:jar:3.3.2:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-starter-logging:jar:3.3.2:compile
[INFO] |  |  |  +- ch.qos.logback:logback-classic:jar:1.5.6:compile
[INFO] |  |  |  |  \- ch.qos.logback:logback-core:jar:1.5.6:compile
[INFO] |  |  |  +- org.apache.logging.log4j:log4j-to-slf4j:jar:2.23.1:compile
[INFO] |  |  |  |  \- org.apache.logging.log4j:log4j-api:jar:2.23.1:compile
[INFO] |  |  |  \- org.slf4j:jul-to-slf4j:jar:2.0.9:compile
[INFO] |  |  +- jakarta.annotation:jakarta.annotation-api:jar:2.1.1:compile
[INFO] |  |  +- org.springframework:spring-core:jar:6.1.11:compile
[INFO] |  |  |  \- org.springframework:spring-jcl:jar:6.1.11:compile
[INFO] |  |  \- org.yaml:snakeyaml:jar:2.2:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-json:jar:3.3.2:compile
[INFO] |  |  +- com.fasterxml.jackson.core:jackson-databind:jar:2.17.2:compile
[INFO] |  |  |  +- com.fasterxml.jackson.core:jackson-annotations:jar:2.17.2:compile
[INFO] |  |  |  \- com.fasterxml.jackson.core:jackson-core:jar:2.17.2:compile
[INFO] |  |  +- com.fasterxml.jackson.datatype:jackson-datatype-jdk8:jar:2.17.2:compile
[INFO] |  |  +- com.fasterxml.jackson.datatype:jackson-datatype-jsr310:jar:2.17.2:compile
[INFO] |  |  \- com.fasterxml.jackson.module:jackson-module-parameter-names:jar:2.17.2:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-tomcat:jar:3.3.2:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:10.1.26:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-el:jar:10.1.26:compile
[INFO] |  |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:10.1.26:compile
[INFO] |  +- org.springframework:spring-web:jar:6.1.11:compile
[INFO] |  |  +- org.springframework:spring-beans:jar:6.1.11:compile
[INFO] |  |  \- io.micrometer:micrometer-observation:jar:1.13.2:compile
[INFO] |  |     \- io.micrometer:micrometer-commons:jar:1.13.2:compile
[INFO] |  \- org.springframework:spring-webmvc:jar:6.1.11:compile
[INFO] |     +- org.springframework:spring-aop:jar:6.1.11:compile
[INFO] |     +- org.springframework:spring-context:jar:6.1.11:compile
[INFO] |     \- org.springframework:spring-expression:jar:6.1.11:compile
[INFO] +- log4j:log4j:jar:1.2.17:compile
[INFO] +- org.slf4j:slf4j-reload4j:jar:2.0.9:compile
[INFO] |  \- ch.qos.reload4j:reload4j:jar:1.2.22:compile
[INFO] +- org.slf4j:slf4j-api:jar:2.0.9:compile
[INFO] \- com.google.code.gson:gson:jar:2.8.9:compile
```
2. 排除冲突的依赖

2.1 直接引用
```
 
```

        <!--

        <dependency>

            <groupId>org.slf4j</groupId>

            <artifactId>slf4j-log4j12</artifactId>

            <version>${slf4j.version}</version>

        </dependency>

        -->

2.2 间接引用

[SLF4J 官方给出的解决冲突的方法:](https://www.slf4j.org/codes.html#multiple_bindings) For example, cassandra-all version 0.8.1 declares both log4j and slf4j-log4j12 as compile-time dependencies.
```
 
```

        <dependency>

          <groupId> org.apache.cassandra</groupId>

          <artifactId>cassandra-all</artifactId>

          <version>0.8.1</version>
         
          <!-- exclusion -->

          <exclusions>

            <exclusion> 

              <groupId>org.slf4j</groupId>

              <artifactId>slf4j-log4j12</artifactId>

            </exclusion>

            <exclusion> 

              <groupId>log4j</groupId>

              <artifactId>log4j</artifactId>

            </exclusion>

          </exclusions>

          <!-- exclusion ok -->

        </dependency>

### POM

#### jar moved

[WARNING] The artifact XXX has been relocated to XXX artifacts moved to reverse-DNS compliant Maven
```
 修改 groupId & artifact
```

#### slf4j

Kafka client 需要 slf4j 日志包，否则会有警告：
```
 SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder"
```

加入 slf4j-nop/slf4j-simple/slf4j-log4j12/slf4j-jdk14/logback-classic 等其中一个 jar 包即可，因为 Sprint Boot 3.3.2 使用的是 logback-classic，所以：
 ```
    ch.qos.logback
    logback-classic
    1.5.6

# src/main/resources/logback.xml
    
// 若要改变文件路径，需要：
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.joran.JoranConfigurator;
import org.slf4j.LoggerFactory;
import java.io.File;
{
   ...
   configureLogback("etc/logback.xml");
   ...
}
private static void configureLogback(String configFilePath) {
    LoggerContext context = (LoggerContext) LoggerFactory.getILoggerFactory();
    try {
        JoranConfigurator configurator = new JoranConfigurator();
        configurator.setContext(context);
        context.reset();
        configurator.doConfigure(new File(configFilePath));
    } catch (Exception e) {
        logerror("Error loading logback configuration file: " + e.toString());
    }
}
```

若加入 slf4j-log4j12，需要配置 log4j.properties，并在类中定义部分加载：
 ```
<!-- log4j -->
    log4j
    log4j
    1.2.17
<!-- slf4j -->
    org.slf4j
    slf4j-log4j12
    2.0.9

# etc/log4j.properties
log4j.logger.org.apache.kafka=ERROR

# KAFKA.java
import org.apache.log4j.PropertyConfigurator;
static {
    PropertyConfigurator.configure("etc/log4j.properties");
}
```
