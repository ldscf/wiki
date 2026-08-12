---
source_title: Shell - rrun
categories:
- Develop
- Linux
- Shell
last_modified: '2025-06-06T00:59:05Z'
---
此脚本用于所有在<机器列表文件>中的主机上远程执行<命令>。
 ```
#!/bin/bash

# This script remotely executes a command on a list of machines.
#

# Usage:

#   rrun  
#

#   : Path to a file containing a list of machine names or IP addresses (one per line).

#   : The command to execute on each machine. This should be quoted to prevent premature expansion.

# Check if the second argument (the command) is provided.
if [ -z "$2" ]; then
    echo "Title  : Remote Run"
    echo "Usage  : rrun  "
    echo "  : Path to a file containing machine names/IPs (one per line)."
    echo "  : The command to execute on each machine (quote it!)."
    echo ""
    echo "Date   : 2018-11-15"
    echo "Author : adaM (ldscfe@gmail.com)"
    exit 0
fi
machine_list_file="$1"
command_to_execute="$2"

# Check if the machine list file exists and is readable.
if [ ! -r "$machine_list_file" ]; then
    echo "Error: Machine list file '$machine_list_file' not found or not readable." >&2
    exit 1
fi

# Executes a command on remote machines.
while IFS= read -r machine; do
    echo "--- Running on: $machine ---"
    ssh -n "root@$machine" "$command_to_execute"
done < "$machine_list_file"
exit 0
```

### 注意

用 ssh 在远程主机上执行命令中的选项 -n 不可缺少，原因在于 ssh 命令默认会从标准输入（stdin）读取数据，而 while 循环正是从文件中读取机器 IP。执行 ssh 命令时，它会“抢走”本该留给循环后续迭代的输入，从而导致循环在第一台主机执行后就结束。
- 使用 ssh 的 -n 选项：该选项会使 ssh 不从标准输入读取数据
- 将标准输入重定向到 /dev/null，如：ssh "root@$machine" "$command_to_execute" < /dev/null
