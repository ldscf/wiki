---
source_title: Shell - port monitor
categories:
- Develop
- Linux
- Shell
last_modified: '2026-01-20T02:58:26Z'
---
此脚本用于检查特定的 TCP 端口是否在监听。如果某个端口不可用，则使用相应的脚本重启服务。

定时任务可以放在 crontab 中，macOS 下也可以使用 [[LaunchAgent]]。

### 参数
1. 端口配置 (PORTS)：(1080 23389 23390 23391)
1. 脚本命名规则 (SCRIPT_DIR，CMD)：$HOME/scripts/monitor/port_monitor_端口号.sh
1. 日志记录 (LOG)：$HOME/log/port_monitor.log

### 说明
- 将手动执行的脚本与自动化/系统级的脚本（如定时任务）分开存放，参考 [[wikipedia:Filesystem_Hierarchy_Standard|FHS (Filesystem Hierarchy Standard)]] 标准。
- 在 Shell 脚本中，如果将 else 下代码全部注释掉，脚本会报错。加入一个冒号 :（代表空操作 No-op）可以避免语法错误。
 ```
#!/bin/bash
 
 # ==============================================================================
 # Script Name:   port_monitor.sh
 # Description:   Multi-port monitoring and auto-restart service script.
 # Author:        Adam Lee(ldscfe@gmail.com)
 # Date:          2026-01-15
 # Version:       1.0.0
 # License:       MIT
 # Compatibility: macOS (BSD), Linux (GNU)
 # ==============================================================================
 
 # --- Configuration ---
 # Target ports to monitor
 PORTS=(1080 23389 23390 23391)
 
 # Dedicated directory for startup scripts
 SCRIPT_DIR="$HOME/scripts/monitor"
 
 # Path to the unified log file
 LOG_FILE="$HOME/log/port_monitor.log"
 
 # --- Initialization ---
 mkdir -p "$SCRIPT_DIR"
 mkdir -p "$(dirname "$LOG_FILE")"
 
 # --- Global Parameter Check ---
 # Check if "FILE" argument is passed to the script for silent logging
 DO_LOG_FILE=false
 for arg in "$@"; do
     [ "$arg" == "FILE" ] && DO_LOG_FILE=true
 done
 
 # --- Color Definitions (Cross-platform) ---
 RED='\033[0;31m'             # Error
 GREEN='\033[0;32m'           # SUCCESS
 YELLOW='\033[0;33m'
 GRAY='\033[0;37m'            # 
 DARK_GRAY='\033[1;30m'       # ACTION
 NC='\033[0m'                 # No Color
 
 # --- Unified Log Function ---
 log() {
     local msg="$1"
     local level="${2:-INFO}"
     local color=$NC
 
     case "$level" in
         "ACTION")  color=$DARK_GRAY ;;
         "ERROR")   color=$RED       ;;
         "SUCCESS") color=$GREEN     ;;
         *)         color=$NC        ;;
     esac
 
     local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
     
     if [ "$DO_LOG_FILE" = true ]; then
         # Silent Mode: Formatted text to log file
         printf "%s: [%s] %s\n" "$timestamp" "$level" "$msg" >> "$LOG_FILE"
     else
         # Interactive Mode: Colored output to terminal
         printf "%b%s: [%s] %s%b\n" "$color" "$timestamp" "$level" "$msg" "$NC"
     fi
 }
 
 # --- Main Monitoring Loop ---
 for PORT in "${PORTS[@]}"; do
     CMD="$SCRIPT_DIR/port_monitor_${PORT}.sh"
     
     # Check if the port is active using lsof
     if ! lsof -nP -iTCP:$PORT -sTCP:LISTEN > /dev/null; then
         
         log "Port $PORT is inactive." "ACTION"
         
         if [ -f "$CMD" ]; then
             # Capture combined output of the startup script
             output=$(/bin/bash "$CMD" 2>&1)
 
             # Check exit status immediately after command execution
             if [ $? -eq 0 ]; then
                 log "Port $PORT started. ${output:+ Info: $output}" "SUCCESS"
             else
                 log "Port $PORT failed. ${output:+ Error: $output}" "ERROR"
             fi
         else
             log "Port $PORT: $CMD not found." "ERROR"
         fi
     else
         # Optional: log "Port $PORT is active."
         : 
     fi
 done
```

### 技术列表

| 标题 | 语法 | 功能 |
| 路径自动化 | $(dirname "$LOG_FILE") | 动态提取文件所在目录，配合 mkdir -p 实现自动初始化，防止因目录缺失报错。 |
| 参数扫描 | for arg in "$@" | 遍历脚本所有输入参数，通过检测 FILE 标记实现运行模式切换（手动 vs 定时任务）。 |
| 默认值缺省 | ${2:-INFO} | 变量位扩展：如果调用函数时未提供第二个参数（级别），则自动赋值为 INFO。 |
| 网络状态检查 | lsof -nP -iTCP:$PORT | 非阻塞式检查端口：-nP 禁用域名/端口名解析（极快），准确判断 LISTEN 状态。 |
| 条件后缀扩展 | ${output:+ Info: $output} | 参数扩展技术：仅当变量 output 不为空时，才显示其内容。这保证了日志的简洁美观，避免空标签。 |
| 状态码追踪 | if [ $? -eq 0 ] | 实时捕获上一个命令（子脚本启动）的退出状态码，作为判断执行成功或失败的唯一标准。 |
| 空指令占位 | : | Shell 的 No-op 内置指令。在逻辑分支中作为占位符，保持代码结构完整且不执行任何操作。 |
