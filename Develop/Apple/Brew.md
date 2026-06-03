---
source_title: Brew
categories:
- Apple
- Develop
- Mac
last_modified: '2026-05-19T07:57:32Z'
---
**Homebrew** 是 macOS 上最受欢迎的包管理器。它让用户能够通过一行命令，轻松安装、更新和卸载原本需要去官网下载的各类软件（包括终端工具和日常使用的 App）。

### 概念

在 Homebrew 的世界里，所有的专业术语都和“酿酒”有关：
* **Formula (配方)**：传统的命令行工具或开发库（例如 git, python, node）。
* **Cask (木桶)**：带有图形界面 (GUI) 的 macOS 应用程序（例如 iterm2, google-chrome, visual-studio-code）。
* **Tap (酒嘴/水龙头)**：第三方软件仓库。默认使用的是官方仓库，但可以通过 brew tap 接入别人的仓库。
* **Cellar (酒窖)**：软件实际在本地被安装的存放路径。
* **Leaves (树叶)**：没有被其他任何软件所依赖的独立软件（也就是用户主动安装的那些）。

### 基础命令

#### 搜索与安装
 ```
brew search <软件名>          # 搜索软件（同时搜索 Formula 和 Cask）
brew install <软件名>         # 安装命令行工具 (Formula)
brew install --cask <软件名>  # 安装桌面应用 (Cask)
```

#### 查看已安装软件
 ```
brew list                  # 列出所有已安装的软件
brew list --formula        # 只列出命令行工具
brew list --cask           # 只列出桌面应用
brew leaves                # 只列出主动安装的主程序（不含被动下载的底层依赖库）
```

#### 更新与升级
 ```
brew update                # 更新 Homebrew 自身以及软件仓库索引
brew outdated              # 列出本地哪些软件已经有新版本了
brew upgrade               # 升级所有有新版本的软件
brew upgrade <软件名>       # 仅升级某一个特定的软件
```

### 干净卸载与磁盘大扫除

#### 卸载主程序
 ```
brew uninstall <软件名>          # 卸载命令行工具
brew uninstall --cask <软件名>   # 卸载桌面应用
```

#### 深度卸载

对于 Cask 桌面应用，如果想把它产生的缓存、注册表等彻底从 Library 里拔除：
 ```
brew uninstall --cask --zap <软件名>
```

#### 自动清理垃圾

当删除了某些主程序，它们当初下载的底层依赖库就变成了系统垃圾（孤儿库）。
 ```
brew autoremove  # 自动找出并卸载所有已经没人使用的底层依赖库
brew cleanup     # 清理下载的旧版本安装包缓存，释放空间
```

### 高级进阶技巧

#### 一键备份与恢复

当需要换新 Mac，或者重装系统时，不需要重新一个个敲命令安装软件。
1. **在旧电脑上导出当前所有软件清单：**
1. :  ```

brew bundle dump --global
```
#: 这会在家目录 (~) 下生成一个 .Brewfile。里面记录了所有的 formula, cask 以及 App Store 软件。

# **在新电脑上原封不动地一键恢复：**
#: 将该文件拷贝到新电脑的相同目录下，运行：
#:  ```
brew bundle --global
```
1. : 新电脑就会自动开始批量下载并安装所有软件，实现完美迁移。

#### 处理后台服务

很多后端组件（如 mysql, redis, nginx）在安装后需要作为后台服务常驻运行。Homebrew 内置了管理工具：
 ```
brew services list               # 查看当前有哪些后台服务在运行
brew services start <服务名>      # 启动某个服务（并设置开机自启）
brew services stop <服务名>       # 停止某个服务
brew services restart <服务名>    # 重启某个服务
```

*示例：启动安装的 Redis：* brew services start redis@6.2

#### 故障排查

如果某天 Homebrew 报错、或者下载卡住，可以用这两个命令进行体检：
 ```
brew doctor     # 诊断命令，检查系统环境，会给出修复建议
brew config     # 查看当前 Homebrew 的配置信息（包括各种路径和环境变量）
```

### macOS 上的默认安装路径

Homebrew 会把软件集中管理，不同架构的 Mac 路径有所不同：
* **Apple Silicon 芯片 (M1/M2/M3/M4 系列 Mac)：**
  - 主要安装在 /opt/homebrew/ 目录下。
* **Intel 芯片 Mac：**
  - 主要安装在 /usr/local/Cellar/ 目录下。

From Google Gemini 3.1 Fast free
