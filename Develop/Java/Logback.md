---
source_title: Logback
categories:
- Develop
- Java
last_modified: '2025-07-29T08:18:47Z'
---
Logback 是由 Log4j 作者开发的现代化日志框架，是 SLF4J 的原生实现，具有高性能、配置简洁、安全性强的特点。
- 输出日志到控制台、文件、滚动文件、Syslog 等多种目的地
- 灵活定义日志格式和级别
- 基于时间或大小的日志滚动策略
- 多级日志控制（ALL < TRACE < DEBUG < INFO < WARN < ERROR < OFF）

### pom.xml
```
 *```
```

<dependencies>

    <!-- 日志 API 接口 -->

    <dependency>

        <groupId>org.slf4j</groupId>

        <artifactId>slf4j-api</artifactId>

        <version>2.0.9</version>

    </dependency>

    <!-- Logback 实现 -->

    <dependency>

        <groupId>ch.qos.logback</groupId>

        <artifactId>logback-classic</artifactId>

        <version>1.4.14</version>

    </dependency>

</dependencies>

```*

### logback.xml

src/main/resources/logback.xml
```
 *```
```

<configuration>

    <!-- 控制台输出 -->

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">

        <encoder>

            <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{36} - %msg%n</pattern>

        </encoder>

    </appender>

    <!-- 文件输出 + 滚动策略 -->

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <file>logs/app.log</file>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>

            <maxHistory>7</maxHistory>

        </rollingPolicy>

        <encoder>

            <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{36} - %msg%n</pattern>

        </encoder>

    </appender>

    <!-- 默认日志级别 -->

    <root level="INFO">

        <appender-ref ref="CONSOLE"/>

        <appender-ref ref="FILE"/>

    </root>

    <!-- 指定包或类日志级别 -->

    <logger name="org.mybatis" level="DEBUG"/>

    <logger name="java.sql" level="DEBUG"/>

    <logger name="org.apache.kafka" level="ERROR"/>

</configuration>

```*

### 输出日志类

## See Also
1. [logback 官网](https://logback.qos.ch)
1. [ttps://www.slf4j.org SLF4J 官网]
