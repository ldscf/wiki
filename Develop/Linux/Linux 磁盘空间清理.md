---
source_title: Linux 磁盘空间清理
categories:
- Develop
- Linux
- Shell
last_modified: '2025-01-07T03:04:33Z'
---
### Snap

Snap 会保留以前安装/卸载的软件包的旧版本。保留的版本数量，默认值为 3，包括当前安装版本。
- 修改保留版本数量: snap set system refresh.retain=2
- 保留的软件包列表: snap list --all

清理 Snap 的脚本:
 ```
#!/bin/bash
 # Removes old revisions of snaps
 # CLOSE ALL SNAPS BEFORE RUNNING THIS
 set -eu
 LANG=en_US.UTF-8 snap list --all | awk '/disabled/{print $1, $3}' |
     while read snapname revision; do
         snap remove "$snapname" --revision="$revision"
     done
```

See Also: [How to Clean Up Snap Versions to Free Up Disk Space](https://www.debugpoint.com/clean-up-snap/)

### Log

/var/log/journal
 ```

# ./rrun mc.lis 'du -ms /var/log/journal'

# vi /etc/systemd/journald.conf
SystemMaxUse=16M
ForwardToSyslog=no
systemctl restart systemd-journald.service
journalctl --verify
```
