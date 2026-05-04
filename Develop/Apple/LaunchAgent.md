---
source_title: LaunchAgent
categories:
- Apple
- Develop
- Mac
last_modified: '2025-12-30T07:20:43Z'
---
LaunchAgent 是 macOS 中 launchd 进程管理体系的一部分，用于为“当前登录用户”管理和启动后台任务或用户级服务。
- 随用户登录而加载（仅对当前用户生效、登录后自动加载）
- 运行在用户权限（非 root）
- 适合启动 GUI 相关、用户态脚本、常驻辅助进程

采用 launchd 统一管理进程，取代了传统的 rc.d / cron / init / xinetd，专注于“用户会话内”的自动化任务。

**launchd 体系**

| 类型 | 作用域 | 权限 | 是否随用户 |
|:---|:---|:---|:---|
| **LaunchAgent** | 用户级 | 当前用户 | 是 |
| LaunchDaemon | 系统级 | root | 否 |
| StartupItem（已废弃） | 系统级 | root | 否 |
LaunchAgent 以 **plist 文件** 的形式存在。
1. 用户下路径：~/Library/LaunchAgents/
1. 全部用户下路径：/Library/LaunchAgents/

### Example

检查 1080 端口是否激活。若无，则初始化一个新的 Shell 会话以建立。

#### 监控脚本
 ```
LOG="$HOME/log/mc2_1080_monitor.log"
CMD="$HOME/bin/mc2_1080.sh"

# Check if port 1080 is active; if not, attempt to start the service
if ! lsof -i :1080 > /dev/null; then
    echo "$(date): [ACTION] Port 1080 is inactive, attempting to start service." >> "$LOG"
    
    # Execute the script and capture both stdout and stderr
    sh "$CMD" >> "$LOG" 2>&1
    
    # Check the exit status ($?) of the script execution
    if [ $? -eq 0 ]; then
        echo "$(date): [SUCCESS] ${CMD} executed successfully" >> "$LOG"
    else
        echo "$(date): [ERROR] ${CMD} failed to execute with exit code $?" >> "$LOG"
    fi
#else

#    echo "$(date): [INFO] Port 1080 is running" >> "$LOG"
fi
```

#### LaunchAgent 配置
 ```

# /Users/lds = /Users/$(whoami)

# ~/Library/LaunchAgents/com.user.check1080.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    Label
    com.user.check1080
    ProgramArguments
    
        /bin/bash
        /Users/lds/scripts/check_1080.sh
    
    RunAtLoad
    
    StartInterval
    60
    StandardOutPath
    /Users/lds/log/check1080.log
    StandardErrorPath
    /Users/lds/log/check1080.err
```

#### 启动任务
```
 launchctl load $PL
```
 
```
 # PL="~/Library/LaunchAgents/com.user.check1080.plist"
 # 停止监控: launchctl unload $PL
 # 查看状态: launchctl list | grep check1080      # 返回数字是 0，说明执行成功
```
