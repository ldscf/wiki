---
source_title: MacOS
categories:
- Apple
- Develop
- Mac
last_modified: '2026-09-04T01:41:24Z'
---
macOS 是 Mac OS X（后来称为 OS X，发音为 oh-ess-ten）的延续，由苹果开发的运行于 Macintosh 系列电脑上的操作系统，最初发布于 2001 年。基于 Unix 构建，与 Unix 和 Linux 有许多底层相似之处，如多用户支持、抢占式多任务处理以及使用终端访问等。

### 计算机名称
```
 scutil --set ComputerName "MacPro-Adam"
 scutil --set HostName "MacPro-Adam"
```

### 加载证书
```
 # ssh-add -L        # list all
 # ssh-add -D        # delete all
 # ssh-add -d KEY    # delete KEY
 # ssh-add -K KEY    # 将密钥的密码存储到 Keychain 中，重启有效
 # -t 3600           # 密钥有效期为 1 小时
 ssh-add bi_bg.pem   # Add key
```

### 路由
```
 # 添加
 route -n add -net 10.10.0.0 -netmask 255.255.255.0 192.168.0.121
 # vpn: 10.10.0.100, en0: 192.168.0.121
```

### 配置选项
```
 source ~/.zshrc
```

**空格隐藏命令**
```
 setopt HIST_IGNORE_SPACE
```

命令前加空格则不记录到历史记录

**别名**
```
 alias ll='ls -lhG'
```

**颜色**
```
 export LSCOLORS="ExfxCxdxCxegedabagacad"
 export CLICOLOR=1
```

| 位置 | 代表类型 | 建议设置 | 颜色效果 |
|:---|:---|:---|:---|
| 1-2 | 目录 (Directory) | Ex | 蓝色 |
| 3-4 | 软链接 (Symlink) | fx | 青色 (浅蓝色) |
| 5-6 | 套接字 (Socket) | cx | 绿色 |
| 7-8 | 管道 (Pipe) | dx | 棕色/暗红 |
| 9-10 | 可执行文件 (Executable) | Cx | 加粗绿色 |

### Tools

#### brew

在 macOS 上，使用 Homebrew 可以通过命令行安装各种开发工具。
```
 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
 # 或者使用镜像源
 /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```
 
```
 brew --version
 brew install python@3.10      # 实际安装的是 Python 3.10.19
```

#### Windows App

远程桌面，MacOS App Store。

#### Rosetta

Rosetta 2 是苹果公司为搭载 Apple Silicon（M1、M2、M3 等系列芯片） 的 Mac 电脑开发的一款动态二进制翻译器，解决 x86 软件兼容性问题。> Rosetta 2 在后台自动、无缝地将为 Intel x86-64 指令集编译的应用程序，翻译成 Apple Silicon 芯片能理解的 ARM64 指令集，从而可以运行那些尚未针对新芯片进行优化的应用。

初代 Rosetta 用于帮助 PowerPC 应用在 Intel Mac 上运行。安装：一般是第一次尝试打开一个 Intel 的应用程序时，macOS 会自动弹出对话框提示安装。

#### X 窗口

[Xquartz](https://www.xquartz.org/) 远程连接 Linux 的 X 窗口系统。

The XQuartz project is an open-source effort to develop a version of the X.Org X Window System that runs on macOS. 
```
 # Mac
 ssh -X @
 # remote
 # export DISPLAY=localhost:10.0     # 从 XQuartz 终端 ssh 默认自动配置
 export LANG=us_EN.UTF-8
 xclock
```

#### Markdown

[MarkText](https://github.com/marktext/marktext): 开源 Markdown 编辑器

A simple and elegant open-source markdown editor that focused on speed and usability.

#### LaTex

Formula Editor: 公式编辑器（数理化），MacOS App Store。

提供大量预置公式仕样，输入后下方实时展示，直接拷贝输入框中的 LaTex 代码使用即可。

### 技巧

#### 截图阴影
```
 # Command + Shift + 5
 # Mac 自带的截图会自动添加阴影
 关闭: defaults write com.apple.screencapture disable-shadow -bool true
```

#### textutil

textutil 是一个系统自带的，用于处理文稿的命令，允许将任何文件，在以下文件格式中互相转换 txt, html, rtf, rtfd, doc, docx, wordml, odt, webarchive。
```
 textutil -convert txt $FILENAME
```

### Err

#### 触摸板手势异常

打开【Activity Monitor / 活动监视器】，找到【Dock / 程序坞】将其强制退出。该方法可使 Dock 进程重启，触摸板手势异常亦随之消失。

#### obsidian

打开和编辑缓慢，占内存大几百 M，可以删除如下目录
```
 /Users/ldscf/Library/Application\ Support/obsidian
```

app.json 打开报错（Cmd + Option + I，可以看到）
```
 <笔记目录>/.obsidian/app.json
```

### QA

#### iWatch 解锁
1. 启用“解锁 Mac”功能  

（Mac）系统设置 -> Touch ID 与密码/安全性与隐私 -> 勾选 “使用 Apple Watch 解锁 Mac”
1. Apple ID 启用双重认证
1. 开启 蓝牙 & Wi-Fi  

若忽然不可用，重启手机蓝牙
