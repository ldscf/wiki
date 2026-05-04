---
source_title: Java 概述
categories:
- Develop
- Java
last_modified: '2025-07-29T06:25:38Z'
---
Java 是一种由 Sun Microsystems（2009 年被 Oracle 公司收购）于 1995 年推出、基于面向对象编程（OOP）思想的编程语言和计算平台。它以其 WORA（Write Once, Run Anywhere，一次编写，随处运行） 的能力而闻名，这意味着编译后的 Java 代码可以在任何支持 Java 虚拟机（JVM）的设备上运行，而无需重新编译。

#### 体系

| 名称 | 全称 | 全称 (中文) | 旧称 |
|:---|:---|:---|:---|
| + |
| JavaEE | Java 2 Platform Enterprise Edition | Java 平台企业版 | J2EE |
| JavaSE | Java 2 Platform Standard Edition | Java 平台标准版 | J2SE |
| JavaME | Java 2 Platform Micro Edition | Java 平台微型版 | J2ME |

#### 平台组成

| 名称 | 全称 | 全称 (中文) | 说明 |
|:---|:---|:---|:---|
| + |
| JVM | Java Virtual Machine | Java 虚拟机 | Java 平台的核心，负责解释和执行 Java 字节码。JVM 将字节码转换为底层操作系统的机器指令 |
| JDK | Java Development Kit | Java 开发工具包 | 包括 JRE、编译器、调试工具、文档生成工具（javadoc）等。 |
| JRE | Java Runtime Environment | Java 运行环境 | 运行 Java 应用程序所需的一切，包括 JVM、Java 核心类库和支持文件 |

#### 特性

| 名称 | 英文 | 说明 |
|:---|:---|:---|
| + |
| 面向对象 | Object-Oriented | Java 严格遵循面向对象原则，支持封装、继承、多态和抽象。这使得代码结构清晰、模块化、易于维护和扩展 |
| 平台独立性 | Platform Independent | Java 重要特性。Java 源代码被编译成平台无关的字节码（Bytecode），可以在任何安装了 Java 虚拟机（JVM）的环境上运行 |
| 简单性 | Simple | 移除了 C++ 中一些复杂和容易出错的特性（如指针、多重继承、运算符重载等），使得语言更易于学习和使用 |
| 安全性 | Secure | 通过 JVM 的沙箱（Sandbox）机制、字节码校验等，提供了健壮的安全模型，防止恶意代码对系统造成破坏 |
| 健壮性 | Robust | 拥有强大的内存管理（通过垃圾回收机制，GC）和异常处理机制，这大大减少了程序崩溃的可能性，提高了程序的稳定性 |
| 多线程 | Multithreaded | 内置对多线程编程的支持，允许同时执行程序的多个部分，充分利用 CPU 资源，提高应用程序的性能和响应速度 |
| 高性能 | High Performance | JVM 包含即时编译（JIT, Just-In-Time）技术，可以编译成本地机器码，从而显著提高运行速度，使其性能接近原生代码 |
| 分布式 | Distributed | 提供了丰富的网络编程功能（如 RMI, CORBA 等），方便开发分布式应用程序 |
| 动态性 | Dynamic | 可以在运行时动态地加载类文件 |

#### 应用领域

Java 的 WORA 特性使其在企业级应用中占据主导地位，但其应用远不止于此：
* **Android 移动应用开发**：绝大多数 Android 应用程序都是用 Java 编写的（尽管 Kotlin 也在崛起）
* **Web 应用后端开发**：使用 Spring, Spring Boot, Hibernate, Struts, JSP, Servlet 等框架构建企业级 Web 应用的后端服务
* **企业级应用**：广泛用于大型、复杂的企业系统，如银行、金融服务、电子商务、大数据处理等
* **大数据技术**：Hadoop, Spark 等许多大数据处理框架都是用 Java（或 Scala，运行在 JVM 上）编写的
* **桌面应用**：使用 Swing, JavaFX 等库开发桌面 GUI 应用程序
* **科学应用**：许多科学计算和研究工具都使用 Java
* **嵌入式系统**：在一些嵌入式设备、IoT（物联网）设备中也有应用
* **游戏开发**：Minecraft（我的世界）就是用 Java 编写的
