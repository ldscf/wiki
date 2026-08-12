---
source_title: IntelliJ IDEA
categories:
- Develop
- Java
- Tools
last_modified: '2026-01-06T01:52:18Z'
---
IntelliJ IDEA 2023

#### jar 包

文件(File) -> 项目结构(Project Structure) -> 项目设置(Project Settings) -> 工件(Airifacts) -> + -> jar -> 来自依赖
- 主类（需要 jar 包默认可执行时，需输入指定名称）
- +包含测试（否则无法执行主类的 main）
- 来自库的 jar 文件 -> 提取到目标（打包成一个文件, Extract to the Taget JAR），复制到输出...(多个文件)，该选项后面的“输出布局”中可以在主类中选择“类路径”，实现将相关 jar 包放在不同目录的效果。

#### Git
```
 IDEA -> Preferences -> Version Control
 -> Git
```

  Path to Git executable =     # 设置 Git 所在路径
```
 -> GitHub
```

  Log in via GitHub

#### POM

可在 [Maven 仓库](https://mvnrepository.com/) 查询包及版本。[Maven 官方中央仓库](https://search.maven.org/)
```
 *```
```

<project xmlns="http://maven.apache.org/POM/4.0.0"

         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0

                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.aaa.udef</groupId>

    <artifactId>udefapi</artifactId>

    <version>1.0.0</version>

    <name>udef-api</name>

    <url>https://www.mwbbs.tk</url>

    <description>UDF API Class</description>

    <packaging>jar</packaging>

    <properties>

        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <maven.compiler.source>17</maven.compiler.source>

        <maven.compiler.target>17</maven.compiler.target>

    </properties>

    <dependencies>

        <!-- 日志 API 与桥接实现 -->

        <dependency>

            <groupId>org.slf4j</groupId>

            <artifactId>slf4j-api</artifactId>

            <version>2.0.9</version>

        </dependency>

        <dependency>

            <groupId>ch.qos.logback</groupId>

            <artifactId>logback-classic</artifactId>

            <version>1.5.6</version>

        </dependency>

        <!-- 其他依赖声明 -->

    </dependencies>

</project>

```*

##### 依赖声明
```
 *```
```

<!--mysql-->

<dependency>

    <groupId>com.mysql</groupId>

    <artifactId>mysql-connector-j</artifactId>

    <version>8.0.33</version>

</dependency>

<!--h2-->

<dependency>

    <groupId>com.h2database</groupId>

    <artifactId>h2</artifactId>

    <version>2.1.212</version>

</dependency>

<!--Json-->

<dependency>

    <groupId>com.google.code.gson</groupId>

    <artifactId>gson</artifactId>

    <version>2.8.9</version>

</dependency>

<!--Kafka-->

<dependency>

    <groupId>org.apache.kafka</groupId>

    <artifactId>kafka-clients</artifactId>

    <version>2.0.0</version>

</dependency>

<dependency>

    <groupId>org.apache.kafka</groupId>

    <artifactId>kafka_2.11</artifactId>

    <version>0.10.0.1</version>

</dependency>

<dependency>

    <groupId>org.apache.kafka</groupId>

    <artifactId>kafka-streams</artifactId>

    <version>1.0.0</version>

</dependency>

```*

#### .iml

##### .idea

.idea 文件夹是存储 IntelliJ IDEA 项目的配置信息，主要内容有项目本身的一些编译配置、文件编码信息、jar 包的数据源和相关的插件配置信息。此类信息属于本地配置信息，无需提交到版本控制。

##### .iml

根目录下的 .iml 可以定义 IntelliJ IDEA 项目的显示名称、源代码目录等，覆盖 .idea 相关文件(如：udefj2.iml)，如将 udefj2 项目显示为：udef(根目录下的文件 udef.iml)。

##### ~/.m2/repository

pom 中依赖的 jar 下载位置
```
 Preferences -> Build,Execution,Deployment -> Build Tools -> maven -> Local repository
```

#### Git

将一个 Git 管理的项目导入 IDEA 后，可以方便查看版本的差异（包括未提交的 local 版本）：
```
 文件(右键) -> Git -> Show Diff
```

#### Plugins

##### Lingma

通义灵码是由阿里云提供的免费智能编码辅助工具，提供代码智能生成、智能问答、多文件修改、编程智能体等能力。
- 兼容 VS Code、Visual Studio、JetBrains IDEs 等 IDE；
- 支持 Java、Python、Go、C/C++、JavaScript、TypeScript、PHP、Ruby、Rust、Scala 等编程语言。

集成阿里巴巴 Java 开发手册，自动检查代码质量，对 Java（尤其是 Spring 框架）的支持非常出色。

##### Github Copilot

IDEA plugins 中安装（1.5.30-242）后重启

登录 Git 帐号

Responses are limited to 2,000 code completions and 50 chat messages per month.

##### [Continue](https://www.continue.dev/)
```
 *```
```

   ______                   __     _

  / ____/ ____    ____   __/ /_   (_)  ____    __  __  _____ 
```
 / /     / __ \  / __ \ /_  __/  / /  / __ \  / / / / /____/  
```

/ /___  / /_/ / / / / /  / /_   / /  / / / / / /_/ / /____/

\____/  \____/ /_/ /_/   \__/  /_/  /_/ /_/  \__,_/  \___/ 

```*

使用 Continue 进行 AI 辅助代码编写。（备注：新版本已经很垃圾了，很不支持免费方式了，不要使用了）

IDEA 2024.02, Continue 0.0.88, MacOS 12.7.6

# [⌘ J] Ask a question about code

# [⌘ I] Edit code

以下配置实际修改的文件：~/.continue/config.json，需要重启。
 ```
1. 在 IDEA plugins 中安装 continue
2. 配置本地 LLM 模型
   2.1 在 IDEA 右侧工具栏打开 Continue
   2.2 Open Configuration file
   2.3 在 models 项中填加 OpenRouter ApiKey
     {
       "title": "Gemini 2.0 Flash Lite",
       "provider": "openrouter",
       "model": "google/gemini-2.0-flash-lite-preview-02-05:free",
       "apiKey": "sk-or-v1-b5ecc1909...",
       "apiBase": "https://openrouter.ai/api/v1",
       "systemMessage": "You are an expert software developer. You give helpful and concise responses."
     }
     ...
   与 models 并列的项：
   "tabAutocompleteModel": {
     "title": "Gemini 2.0 Flash Lite",
     "provider": "openrouter",
     "model": "google/gemini-2.0-flash-lite-preview-02-05:free",
     "apiKey": "sk-or-v1-b5ecc1909...",
     "apiBase": "https://openrouter.ai/api/v1"
   }
3. systemMessage 是给 AI 模型的指令, 让 AI 更好地理解需求。
4. OpenRouter API 允许为每个模型设置一些参数：
   {
     "title": "Gemini 2.0 Flash Lite",
     ...
     "options": {
       "temperature": 0.7,  // 控制创造性, 0.0 - 1.0, 越低越确定
       "top_p": 0.9,        // 控制多样性, 0.0 - 1.0, 越低越集中
       "max_tokens": 2048   // 控制输出的最大长度
     }
   }
```

##### Generate JavaDoc

Tools -> Generate JavaDoc

# JavaDoc Scope: Custom scope: All Places

# Output directory

# Visibility level: protected, 下面全选

在输出目录中找到 index.html 打开

#### QA

##### 无法在 src 下建立类文件

文件 -> 项目结构 -> 项目设置 -> 模块
```
 源：将 src 标识为源代码，将 target 标记为排除
```
 
```
 将 src/main 也标识为 source，引用包体可以省略为：import com.udf.base.CNF(main.com.udf...)
 将 src/test 标识为测试
```

##### java 不支持发行版本 7
```
 可以在 idea Java编译器配置中修改，但会被 pom 覆盖。
 # pom.xml
 
```
     
         
             
                 1.8

                 1.8
             
         
     
```
 
```

##### 项目左侧目录结构消失

删除项目文件夹下的 .idea 文件夹，重打开项目

##### H2 版本兼容性

v2.2.224 生成/修改的 H2 数据文件，对于有些 db tools 不兼容，如 < dbeaver 24.0.4。（v2.1.212 OK）
        
            com.h2database

            h2

            2.1.212
        
v2.2.224 读以前版本的数据文件报错：
```
 Unsupported database file version or invalid file header
```

##### org.junit 不存在

如果 import org.junit.Test 未报错(已加入依赖)，但执行时出现上述报错，可能是执行目录类型错误。
```
 # IDEA 2024
 File -> Project Structure -> Modules -> (Test java Path) -> Mark as 
```

##### spring assistant

关于 IDEA CE(社区版)(2024)没有 spring assistant 插件（[Jetbrains 官网](https://plugins.jetbrains.com/plugin/17911-spring-assistant/)给出的提示是不兼容），想使用这个插件（解决社区版支持 Spring Boot），可以使用 2023 年 六月以前的社区版本。[IDEA 历史版本](https://www.jetbrains.com/zh-cn/idea/download/other.html)

Spring Assistant 1.05 版本的更新时间是：Jun 09, 2023

P.S. 不使用插件 IDEA CE 一样可以开发 Spring Boot 程序。See Also: [Spring_Boot_微服务极简化开发](#Spring_Boot_微服务极简化开发)
