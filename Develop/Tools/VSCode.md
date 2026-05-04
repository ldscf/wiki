---
source_title: VSCode
categories:
- Develop
- Tools
last_modified: '2026-02-28T01:26:15Z'
---
Visual Studio Code is a lightweight but powerful source code editor which runs on your desktop and is available for Windows, macOS and Linux. It comes with built-in support for JavaScript, TypeScript and Node.js and has a rich ecosystem of extensions for other languages and runtimes (such as C++, C#, Java, Python, PHP, Go, .NET). Begin your journey with VS Code with these introductory videos.

Visual Studio Code 是一个轻量级但功能强大的源代码编辑器，可在您的桌面上运行，可用于 Windows，macOS 和 Linux。它内置了对 JavaScript，TypeScript 和 Node 的支持 .js 并具有针对其他语言和运行时（如C++，C#，Java，Python，PHP，Go，.NET）的丰富扩展生态系统。通过这些介绍性视频开始您的 VS Code 之旅。(由：[Bing翻译](https://cn.bing.com/search?q=%E5%BF%85%E5%BA%94%E7%BF%BB%E8%AF%91&FORM=TTAHP1) 提供)

### Setup

Windows: Ctrl = Mac: Command

#### Download
- https://code.visualstudio.com/

#### 设置成中文
1. Ctrl+Shift+P
1. configure Display language

#### 远程开发配置
- 扩展按钮(左侧，或：Ctrl+Shift+X)
- Remote Development
- 增加 SSH
  - Host mc1     HostName mc1      User bi
  - 在 .ssh 目录下增加 id_rsa

**非常耗服务端资源。**

##### C++

打开 SSH 远程目录后，会建立 .vscode 目录。

**工作区 vs 全局：**
* **工作区设置:** 存储在项目根目录下的 .vscode 文件夹中，只影响当前项目。
* **全局设置:** 存储在用户目录下的一个特定位置（取决于操作系统），影响所有 VS Code 实例。

| *文件名* | *作用* | *影响范围* | *是否特定于语言* | *主要配置内容* |
|:---|:---|:---|:---|:---|
| *tasks.json* | *定义和配置任务（Tasks）。任务可以是编译代码、运行测试、执行脚本等任何可以在终端中运行的命令。VS Code 可以运行这些任务，并将其集成到工作流程中（例如，通过快捷键 Ctrl+Shift+B 运行默认构建任务）。* | *工作区（项目）* | *否* | *任务的名称、类型（shell、process）、要执行的命令、命令行参数、问题匹配器（用于解析输出）、分组等。* |
| *launch.json* | *定义和配置调试器（Debugger）。它告诉 VS Code 如何启动和附加到你的程序，以便进行调试。你可以配置不同的调试配置（例如，调试本地程序、附加到远程进程、使用不同的调试器）。* | *工作区（项目）* | *否* | *调试配置的名称、类型（例如 cppdbg、python、node）、请求类型（launch 或 attach）、程序路径、参数、环境变量、调试器特定的选项（例如，GDB 或 LLDB 的设置）。* |
| *c_cpp_properties.json* | *配置 C/C++ 扩展（IntelliSense）的行为。它告诉 IntelliSense 如何解析你的代码，包括头文件搜索路径、预处理器定义、C/C++ 标准、编译器路径等。这会影响代码补全、错误检查、代码导航等功能。* | *工作区（项目） 或 全局（取决于配置位置）* | *是* | *包含路径、编译器路径、C/C++ 标准、IntelliSense 模式、预处理器定义、强制包含的头文件等。* |
| *settings.json* | *存储 VS Code 的各种设置。这些设置可以是全局的（影响所有 VS Code 实例），也可以是工作区特定的（只影响当前项目）。设置涵盖了编辑器的各个方面，包括外观、行为、语言特定设置等。* | *全局 或 工作区（项目）* | *否* | *编辑器行为（例如，字体、主题、缩进）、文件关联、语言特定设置（例如，Python 的 linting 规则）、扩展设置等。* |
**编译**

编译参数在: tasks.json，如指定 -std=c++17。右上角运行“C/C++ 文件”时，“选择调试配置”框中，可以看到 task label: “C/C++: g++ build”。以下内容基本上自动生成，除了特殊的参数，如："-std=c++17"。
 ```
{
    "version": "2.0.0",
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++ build",
            "command": "/usr/bin/g++",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${file}",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}",
                "-std=c++17"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": "build",
            "detail": "Compiler: /usr/bin/g++"
        }
    ]
}
```

**IntelliSense**

编辑时，如在行：std::optional get(const std::string& key) const {

出现错误：不允许使用限定名

可能原因：VS Code 的 IntelliSense（代码感知）功能使用的 C++ 标准设置与实际编译时使用的不一致。IntelliSense 负责提供代码补全、错误提示等功能，它需要知道使用的 C++ 版本才能正确工作。
 ```
{
    "configurations": [
        {
            "name": "mc2ConfigName",         // 配置名称
            "includePath": [
                "${workspaceFolder}/**"      // 包含路径
            ],
            "defines": [],
            "compilerPath": "/usr/bin/g++",  // 你的编译器路径
            "cStandard": "c11",              // C 标准，C++17 发布时，对应的 C 语言标准是 C11
            "cppStandard": "c++17",          // C++ 标准
            "intelliSenseMode": "gcc-arm64", // IntelliSense 模式
        }
    ],
    "version": 4
}
```

#### VSIX
```
 Extensions(Ctrl+Shift+X) -> ...(右上角) -> Install From VSIX...
```

### Plugin

#### 开发
- rust: [rust-analyzer、通义灵码](https://<!-- template: servername -->/wiki/index.php/Rust_%E5%AE%89%E8%A3%85#VSCode)

#### Continue

使用 Continue 进行 AI 辅助代码编写。

OpenRouter 可配置使用免费模型，需要 OpenRouter API Key。

配置文件与用户相关，故可与 IntelliJ IDEA  共用，参考：[Continue](https://<!-- template: SERVERNAME -->/wiki/index.php/IntelliJ_IDEA#Continue)

#### Cline

Cline（原 Claude Dev）是一款集成于 VSCode 的 AI 编程助手，通过智能化手段提升开发效率。它不仅能够实时检查语法错误，还能根据用户需求快速生成或修改代码文件。此外，Cline 还可以通过无头浏览器启动网站，进行交互操作并捕获日志，助力调试和优化。

**模式**
1. Plan：策划模式。制定方案、分析逻辑、回答问题，不改动任何文件。
1. Act ：执行模式。创建/修改代码、运行终端命令。

**指令**
1. @file：精确定位文件。将指定文件的全部内容读入当前的上下文。“请分析一下 @src/main.rs 里的异步循环逻辑是否存在内存泄漏？”
1. @folder：结构化认知目录。读取该目录的文件树结构。“参考 @backend 目录的文件结构，在前端新建对应的接口调用文件。”
1. @problems：诊断与修复。这个指令会将当前 VS Code “问题（Problems）”面板中的所有报错和警告信息喂给模型。“修复 @problems 中提到的错误。”
1. @url：外部知识获取。实时抓取并读取网页内容。“根据 @https://docs.rs/io-uring/latest/io_uring/ 的官方文档，重构我的提交队列逻辑。”

**使用技巧**
1. 上下文管理：当对话太长、Cline 开始变慢或犯错时，开启新对话，并在新任务中 @ 相关文件。
1. Git 预防：在使用 Cline 批量修改前，养成先 Git Commit 的习惯。如果 Cline 改乱了，可以直接通过 Git 回滚。
1. 指定环境：Python 一般会在虚拟环境中安装一些特别的包。“如果要执行代码，/home/bi/python/env_ai 中已经设置好虚拟环境。“

**卸载**
1. 在 VS Code 中卸载插件
1. 清理插件  

~/.vscode/extensions/saoudrizwan.claude-dev-*
1. 清除数据（API Key、对话历史、设置）  

~/Library/Application\ Support/Code/User/globalStorage/saoudrizwan.claude-dev
1. 清理缓存  

cd ~/Library/Application\ Support/Code/User/workspaceStorage/  

find . -name "*saoudrizwan.claude-dev*" -exec rm -rf {} +
