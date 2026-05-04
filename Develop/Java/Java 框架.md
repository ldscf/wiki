---
source_title: Java 框架
categories:
- Develop
- Java
last_modified: '2024-04-07T06:30:11Z'
---

### [Spring](https://spring.io/projects/spring-framework)

毫无疑问，Spring 框架现在是 Java 后端框架家族里面最强大的一个，其拥有 IOC 和 AOP 两大利器，大大简化了软件开发复杂性。并且，Spring 现在能与所有主流开发框架集成，可谓是一个万能框架。

#### Spring MVC

Spring MVC 是一个 MVC 开源框架，用来代替 Struts。它是 Spring 项目里面的一个重要组成部分，能与 Spring IOC 容器紧密结合，以及拥有松耦合、方便配置、代码分离等特点。
- [知乎 - Java架构之路-纯手写spring MVC框架](https://zhuanlan.zhihu.com/p/393381745)
- [廖雪峰 - 手写Spring](https://www.liaoxuefeng.com/wiki/1539348902182944)
- [腾讯 - 从 0 开始手写一个 Spring MVC 框架](https://cloud.tencent.com/developer/article/1189147)

#### Spring Boot

Spring Boot 是 Spring 开源组织下的一个子项目，也是 Spring 组件一站式解决方案，主要是为了简化使用 Spring 框架的难度，简省繁重的配置。

Spring Boot 提供了各种组件的启动器（starters），开发者只要能配置好对应组件参数，Spring Boot 就会自动配置，让开发者能快速搭建依赖于 Spring 组件的 Java 项目。
- [知乎 - 最全面的 SpringBoot 配置文件详解](https://zhuanlan.zhihu.com/p/57693064)
- [20 道 Spring Boot 面试题](https://segmentfault.com/a/1190000016686735)
- [百度 - SpringBoot与Disruptor：实现特快高并发处理的队列系统](https://cloud.baidu.com/article/3014088)

#### Spring Cloud

Spring Cloud 是一系列框架的集合，是目前微服务框架首选，它利用 Spring Boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot 的开发风格做到一键启动和部署。

Spring Cloud 为开发人员提供了工具，以快速构建分布式系统中的一些常见模式（例如，配置管理，服务发现，断路器，智能路由，微代理，控制总线，一次性令牌，全局锁，领导选举，分布式会话，群集状态）。它们可以在任何分布式环境中正常工作，包括开发人员自己的笔记本电脑，裸机数据中心以及 Cloud Foundry 等托管平台。

### Mybatis

iBatis 曾是开源软件组 Apache 推出的一种轻量级的对象关系映射持久层（ORM）框架，随着开发团队转投Google Code 旗下，ibatis 3.x 正式更名为 Mybatis，即：iBatis 2.x, MyBatis 3.x。

### Hibernate

Hibernate 是一个开放源代码的对象关系映射框架，它对 JDBC 进行了非常轻量级的对象封装，它将 POJO 与数据库表建立映射关系，是一个全自动的 orm 框架。Hibernate 可以自动生成 SQL 语句，自动执行，使得 Java 程序员可以随心所欲的使用对象编程思维来操作数据库。

### Dubbo

Dubbo 是阿里巴巴开源的基于 Java 的高性能 RPC 分布式服务框架，现已成为 Apache 基金会孵化项目。使用 Dubbo 可以将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，可用于提高业务复用灵活扩展，使前端应用能更快速的响应多变的市场需求。

### Netty

Netty 是由 JBOSS 提供的一个开源的、异步的、基于事件驱动的网络通信框架，用 Netty 可以快速开发高性能、高可靠性的网络服务器和客户端程序，Netty 简化了网络应用的编程开发过程，使开发网络编程变得异常简单。

### Shiro

Apache Shiro是一个强大而灵活的开源安全框架，它干净利落地处理身份认证，授权，企业会话管理和加密。

### Ehcache

EhCache 是一个纯 Java 的进程内缓存框架，具有快速、精干等特点，是 Hibernate 中默认的 CacheProvider。它使用的是 JVM 的堆内存，超过内存可以设置缓存到磁盘，企业版的可以使用 JVM 堆外的物理内存。

### Quartz

Quartz 是一个基于 Java 的广泛使用的开源的任务调度框架。

### Velocity

Velocity 是一个基于 Java 的模板引擎，简单而强大的模板语言为各种 Web 框架提供模板服务，来适配 MVC 模型。

### jQuery

jQuery 是一个快速、简洁的 JavaScript 框架，它封装 JavaScript 常用的功能代码，提供一种简便的 JavaScript 设计模式，极大地简化了 JavaScript 编程。

### JUnit

JUnit 是一个 Java 语言的单元测试框架，绝大多数 Java 的开发环境都已经集成了 JUnit 作为其单元测试的工具。

### Log4j

Log4j 是 Apache 的一个开源日志框架，通过 Log4j 我们可以将程序中的日志信息输出到控制台、文件等来记录日志。作为一个最老牌的日志框架，它现在的主流版本是 Log4j2。Log4j2是重新架构的一款日志框架，抛弃了之前 Log4j 的不足，以及吸取了优秀日志框架 Logback 的设计。
