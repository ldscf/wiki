---
source_title: Npm
categories:
- Develop
- Web
last_modified: '2026-04-01T09:04:45Z'
---
Node.js 发布于 2009 年 5 月，由 Ryan Dahl 开发，是一个基于 Chrome V8 引擎的 JavaScript 运行环境，使用了一个事件驱动、非阻塞式 I/O 模型，让 JavaScript 运行在服务端的开发平台，它让 JavaScrip t成为与 PHP、Python、Perl、Ruby 等服务端语言平起平坐的脚本语言。

Node.js 对一些特殊用例进行优化，提供替代的 API，使得 V8 在非浏览器环境下运行得更好，V8 引擎执行 Javascript 的速度非常快，性能非常好，基于 Chrome JavaScript 运行时建立的平台， 用于方便地搭建响应速度快、易于扩展的网络应用。

Node可以在不新增额外线程的情况下，依然可以对任务进行并发处理 —— Node.js 是单线程的。它通过事件循环（event loop）来实现并发操作，对此，尽可能的避免阻塞操作，多使用非阻塞操作。

### npm

node package manger

npm 是 Node 的开放式模块登记和管理系统，是 Node.js 包的标准发布平台，用于 Node.js 包的发布、传播、依赖控制，网址：https://www.npmjs.com/

npm 提供了命令行工具，可以方便地下载、安装、升级、删除包，也可以让你作为开发者发布并维护包

#### Installation of Nodejs and npm Process
 ```

# 1. 清理旧仓库
apt remove -y nodejs npm
apt autoremove -y

# 2. 注入 NodeSource v24 (LTS) 仓库

# 目前(2026)最新的长期支持分支
curl -fsSL https://deb.nodesource.com/setup_24.x | bash -

# 3. 执行安装
apt install -y nodejs

# 4. 验证版本
node -v  # 预期输出: v24.x.x
npm -v   # 预期输出: 10.x.x 或更高
```

### Node.js 前端与服务端开发
1. 依赖同步环境：npm install  

根据 package.json，下载安装第三方库
1. 开发与调试环境：npm run dev  

通常在 package.json 的 "scripts" 部分定义
