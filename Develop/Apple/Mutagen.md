---
source_title: Mutagen
categories:
- Apple
- Develop
- Linux
- Mac
last_modified: '2026-07-28T12:17:25Z'
---
Mutagen 是一个高性能的文件同步工具，支持 macOS、Linux 等平台。
- 双向同步（Two-way sync）
- 基于 SSH 通信
- 增量同步
- 支持大规模代码仓库同步
- 适合本地开发环境与远程 Linux 服务器之间同步

**不是那么稳定，需要经常检查。**

### 安装

#### macOS

使用 Homebrew 安装：
```
 brew install mutagen-io/mutagen/mutagen
```
 
```
 mutagen version
```
```
 # Mutagen daemon 会在首次使用时自动启动，也可以手动启动：
 mutagen daemon start
```

#### Ubuntu 22.04

推荐使用官方二进制安装。
```
 https://github.com/mutagen-io/mutagen/releases
 tar -zxvf mutagen_linux_amd64_vX.Y.Z.tar.gz
 mv mutagen /usr/local/bin/
 mv mutagen-agent /usr/local/bin/
```
 
```
 mutagen version
```

### SSH 配置

Mutagen 默认通过 SSH 连接远程主机。建议提前配置 SSH Key免密码登录。

### 创建同步会话

#### 基础同步
 ```
mutagen sync create \
  --name=srds-sync \
  /path/to/local/project \
  user@server:/path/to/remote/project
```

默认同步模式：two-way-resolved，即：
- 两端修改会自动合并
- 无法自动解决的修改产生 conflict

#### Demo
 ```

# 终止，只删除 Mutagen 同步会话，不删除两端已有文件。
mutagen sync terminate srds-sync
mutagen sync create \
  --name=srds-sync \
  --sync-mode=two-way-resolved \
  --ignore="target" \
  --ignore=".git" \
  --ignore=".claude" \
  --ignore=".codex" \
  --ignore="data" \
  --ignore="history" \
  /Users/ldscf/Documents/mc3.en/SRDS \
  bi@mc3.en:/u01/github/SRDS
```

### 常用命令
1. 查看同步会话：mutagen sync list
1. 查看详细状态：mutagen sync monitor srds-sync
1. 暂停：mutagen sync pause srds-sync
1. 恢复：mutagen sync resume srds-sync
1. 终止：mutagen sync terminate srds-sync

### 注意事项
- 首次同步需要扫描全部文件，大型项目可能需要较长时间。
- 建议忽略编译产物、缓存、运行数据目录。
- 不建议忽略项目配置文件（例如 `.gitignore`）。
- 两端同时修改同一个文件可能产生冲突，需要人工处理。
- daemon 可以长期运行，也可以由 Mutagen 自动管理。
