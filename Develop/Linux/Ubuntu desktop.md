---
source_title: Ubuntu desktop
categories:
- Linux
- Ubuntu
last_modified: '2026-01-02T14:05:19Z'
---
### Xrdp

Port: 3389, 3350

#### Install
```
 # xfce4 传统风格
 apt install xrdp xorgxrdp
 apt install xfce4 xfce4-goodies
```
 
```
 # GNOME Shell 桌面
 # apt install ubuntu-desktop
 # apt install xrdp
```
 
```
 # restart
 systemctl restart xrdp
```

重安装可解决忽然连接不上/黑屏等问题。
```
 # Remove xrdp
 apt update
 apt remove --purge xrdp   # 彻底卸载 xrdp
 apt autoremove --purge    # 清理残留的依赖
```

**修改端口**
```
 # /etc/xrdp/xrdp.ini
 port=23389
```

**使用 SSH 隧道 (SSH Tunneling)**
```
 # 无公网端口，安全性等同于 SSH
 port=127.0.0.1:3389
 # 在本地客户端上运行
 ssh -L 23389:127.0.0.1:3389 bi@server
```

#### Error

##### 可连接，但黑屏
```
 # kill 掉用户的所有进程
 pkill -u bi
```

##### 需要将相应用户加入组
```
 # root 可登录，其他用户黑屏
 adduser bi ssl-cert
```
 
```
 /var/log/xrdp.log
 [ERROR] Cannot read private key file /etc/xrdp/key.pem: Permission denied
```
 
```
 # 原因：默认情况下，Xrdp 使用 /etc/ssl/private/ssl-cert-snakeoil.key, 仅对 ssl-cert 用户组成员可读
 -rw-r----- 1 root ssl-cert 1704 Nov 18 05:58 /etc/ssl/private/ssl-cert-snakeoil.key
```

##### /etc/xrdp/startwm.sh

有些 CSDN 上的文档建议作如下操作：
```
 unset DBUS_SESSION_BUS_ADDRESS
 unset XDG_RUNTIME_DIR
 *''test -x /etc/X11/Xsession && exec /etc/X11/Xsession''*
 *''exec /bin/sh /etc/X11/Xsession''*
 # 需要重启服务
```

这个是很危险的行为，容易造成剪贴板无效(未复现)。现象如：xrdp 启动后一段时间剪贴板正常，但过段时间剪贴板失效。

##### kill firefox 进程

Firefox is already running, but is not responding. To use Firefox, you must first close the existing Firefox process, restart your device, or use a different profile.

窗口打开 firefox 时，提示已打开
```
 kill -9 `ps -ef|grep firefox|awk '{print $2}'|xargs`
```

##### SSL_accept: I/O error
```
 systemctl status xrdp
 [ERROR] SSL_accept: I/O error
 [ERROR] trans_set_tls_mode: ssl_tls_accept failed
 ...
 # 以上错误并未影响使用
```

##### 远程桌面一直显示正在配置远程会话
```
 目前发现一种可能的原因是当前客户端问题（其他客户端可以连接），重启当前客户端电脑解决。
```

##### 剪贴板无效
1. 客户端配置 -> 设备和音频 -> 剪贴板模式（双向）
1. 其他远程桌面剪贴板无问题
1. 重启服务端 xrdp 服务
1. 检查 /etc/xrdp/startwm.sh （参考上面条目）
```
 *```
```

## 正常

ps aux |grep xrdp-chansrv

bi       2035284  0.0  0.0  89764  3228 ?        Sl   14:22   0:00 /usr/sbin/xrdp-chansrv

# log

[20250214-14:04:43] [DEBUG] xrdp_mm_connect_chansrv: chansrv connect successful

## 异常

bi       2035284  0.0  0.0      0     0 ?        Z    14:22   0:00 [xrdp-chansrv] <defunct>

# log

[20250214-14:34:05] [ERROR] xrdp_mm_connect_chansrv: error in trans_connect chan

```*

##### 服务启动失败

# /var/log/xrdp.log 不存在或权限问题（xrdp:adm）均可造成

# /etc/xrdp/ 缺失文件或权限问题（重安装时出现）

# [ERROR] xrdp_wm_log_msg: no library name specified in xrdp.ini, please add lib=libxrdp-vnc.so or similar  

### VNC
```
 apt install xfce4 xfce4-goodies xorg dbus-x11 x11-xserver-utils
 apt install tigervnc-standalone-server tigervnc-common
```
```
 # INIT
 vncserver
 # Run
 vncserver :1 -localhost no -geometry 1280x800 -depth 32    # no 允许远程机器连接，默认不允许
 # List
 vncserver -list
 # Exit
 vncserver -kill :1
```

### Xfce4

Xfce 是类 UNIX 操作系统上的轻量级桌面环境，致力于快速与低资源消耗。
```
 # 确保 xrdp 服务正在运行
 apt install xfce4 xfce4-goodies
 # echo "xfce4-session" > ~/.xsession.  # 如果有多个桌面环境
```

#### 关闭锁屏
```
 Applications -> Settings -> Screensaver
 Enable 均关闭
```

### Fcitx5

Fcitx5 是现代、轻量、高兼容性的输入法平台，支持拼音、五笔等多种输入方式，并可在 GTK 应用中正常工作。

#### Install
* apt update
* apt install fcitx5 fcitx5-config-qt fcitx5-frontend-gtk3 fcitx5-frontend-gtk4 fcitx5-frontend-qt5
* apt install language-pack-zh-hans
* apt install fcitx5-chinese-addons
 ```
fcitx5: Fcitx5 输入法框架的核心包。
fcitx5-config-qt: Fcitx5 的配置工具。通常基于 Qt 界面库，用于图形化设置 Fcitx5 的行为、主题、输入法等。
fcitx5-frontend-gtk3/4/5: Fcitx5 的 GTK3/4/5 应用程序前端模块。允许 Fcitx5 在基于 GTK3/4/5 的应用程序（如 GNOME、XFCE 桌面环境下的许多应用）中正常工作，捕获键盘输入并显示候选词窗口。
language-pack-zh-hans: 简体中文语言包。为整个 Ubuntu 系统提供简体中文的本地化支持，包括用户界面翻译、日期时间格式、区域设置等。
fcitx5-chinese-addons: 包含了具体的中文输入法引擎和词库，例如拼音输入法（包括智能拼音、双拼等）、五笔输入法、二笔输入法等。
1. < 1 GB of additional disk space will be used.
```

#### 启动

Fcitx5 需要一些环境变量来告诉应用程序使用它作为输入法，最推荐的方法是使用 im-config（Debian/Ubuntu），然后选择 Fcitx5。这个工具会自动配置好这些环境变量。

如果未配置启动项，需要在进入 Xfce4 后，手动启动：
```
 Applications -> Settings -> Fcitx 5 Configuration
```

#### 自动上屏

fcitx5-wbpy(显示为 五笔拼音 OR Wubi Pinyin) 在默认配置下，在输入完整的四个键后，会将第一个字/词自动上屏，对于非专业选手十分不友好。

# fcitx5-configtool 或通过应用菜单打开“Fcitx 5 配置工具/Fcitx 5 Configuration”

# 在左侧输入法列表中选中 五笔拼音/Wubi Pinyin

# 中间一列按钮会出现有效状态

# 选择下方的 Confiqure  

  1) 第一屏下方近右侧有：Commit after auto select candidates（自动选择后立即上屏）  

  2) 第二屏下方近右侧有：Auto select candidate（自动选择候选词）  

  3) 只需取消勾选第二个选项即可

### Firefox

#### GTK 版本
 ```
add-apt-repository ppa:mozillateam/ppa
apt update
1. 在 Linux 系统中创建一个 APT 偏好文件，在 apt 处理软件包时，对于来自特定源（LP-PPA-mozillateam）的软件包给予非常高的优先级。
echo '
Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001
' | sudo tee /etc/apt/preferences.d/mozillateamppa
apt install firefox
1. 创建 apt 阻止规则（APT 黑名单），在后期的系统更新中，阻止来自 Ubuntu 官方（Snap 相关源）安装 Firefox
echo '
Package: firefox
Pin: release o=Ubuntu*
Pin-Priority: -1
' | sudo tee /etc/apt/preferences.d/nosnapfirefox
```
```
 # 一般默认方法安装的 Firefox 是沙箱版本（会隔离输入法），如果需要中文输入，安装 GTK 版本。
```

如何判断是否沙箱版本:
```
 snap list firefox     # 应无结果
 snap remove firefox   # 删除沙箱版本
```

#### 强制锁定版本
```
 # apt policy firefox          # 查看当前安装版本、可供升级（Candidate）及源（Repository）
 apt-mark hold firefox         # 锁定该版本
 apt-mark showhold             # 确认状态
```

#### AppArmor

AppArmor 是一种强制访问控制（MAC）系统。它为每个应用程序（或服务）定义一个安全配置文件，该配置文件精确地限制了该应用程序可以访问的系统资源（文件、网络接口、权限等）。这被称为“沙盒”或“进程限制”。AppArmor 策略有时可能过于严格，导致 Firefox 的某些功能（如与特定插件、外部下载管理器、或某些特殊文件系统的交互）出现问题或崩溃。
 ```
ln -s /etc/apparmor.d/usr.bin.firefox /etc/apparmor.d/disable/
1. 禁用下次系统启动时加载这个配置文件
apparmor_parser -R /etc/apparmor.d/usr.bin.firefox
1. 用于从内核中立即卸载一个已经加载的 AppArmor 配置文件。
```
