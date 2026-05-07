---
source_title: Log4j
categories:
- Develop
- Java
last_modified: '2024-04-26T06:09:54Z'
---
Apache Log4j is a versatile, industrial-grade Java logging framework composed of an API, its implementation, and components to assist the deployment for various use cases.

https://logging.apache.org/log4j/2.x/
- 控制日志信息输送的目的地是控制台、文件、GUI 组件，甚至是套接口服务器、NT 的事件记录器、UNIX Syslog 守护进程等
- 控制每一条日志的输出格式
- 通过定义每一条日志信息的级别，更加细致地控制日志的生成过程

ALL < DEBUG < INFO < WARN < ERROR < FATAL < OFF

### pom.xml
```
 <!-- https://mvnrepository.com/artifact/log4j/log4j -->
 
```

     log4j

     log4j

     1.2.17
```
 
```

### log4j.properties
```
 log4j.rootLogger=INFO,stdout
 # 第一段表示输出级别, stdout,logfile 表示输出方式，与下面对应
 # log4j.rootLogger=Error,stdout,logfile
```
 
```
 # stdout
 log4j.appender.stdout=org.apache.log4j.ConsoleAppender
 log4j.appender.stdout.Target=System.out
 log4j.appender.stdout.Threshold=DEBUG
 log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
 log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] %m%n
```
 
```
 # logfile
 log4j.appender.logfile=org.apache.log4j.RollingFileAppender
 log4j.appender.logfile.File=log/system.log
 log4j.appender.logfile.MaxFileSize=10mb
 log4j.appender.logfile.Threshold=Error
 log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
 log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] %m%n
```
 
```
 # Level
 log4j.logger.org.apache.kafka=Error
```
 
```
 *# 可以指定类输出级别
 log4j.logger.org.mybatis=DEBUG
 log4j.logger.java.sql=DEBUG
 log4j.logger.java.sql.Statement=DEBUG
 log4j.logger.java.sql.ResultSet=DEBUG
 log4j.logger.java.sql.PreparedStatement=DEBUG
```

 
```
 如 kafka 中会大量输出 debug 信息，可以指定输出级别或关闭(=OFF)：
 log4j.logger.org.apache.kafka.clients=Error
 log4j.logger.org.apache.kafka.common=Error*
```

### 输出日志类
```
 private static final Logger logger = Logger.getLogger(CLASSNAME.class)
 ...
 logger.info("information...")
```
```
 public static void log(Object log1) {
```

     Logger logger = Logger.getLogger(Thread.currentThread().getStackTrace()[2].getClassName());

     logger.info(log1);
```
 }
```

## slf4j
```
 import org.apache.log4j.PropertyConfigurator;
 import org.slf4j.Logger;
 import org.slf4j.LoggerFactory;
```
 
```
 public class BASE {
```

     public static final String VERSION = "v1.21";

     static {

         PropertyConfigurator.configure("config/log4j.properties");

     }

     private static final Logger logger = LoggerFactory.getLogger(BASE.class);
 
     public static void test(Object log1) {

         logger.info(log1.toString());

         logger.warn(log1.toString());

     }
```
 }
```

## See Also
1. [log4j详解及log4j.properties配置](https://www.cnblogs.com/mytJava/p/13143335.html)
