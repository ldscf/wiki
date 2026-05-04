---
source_title: Windows
categories:
- Develop
- Windows
last_modified: '2026-01-12T01:27:43Z'
---
![Windows1.0.jpg](https://mwbbs.eu.org/wiki/images/0/01/Windows1.0.jpg)

Microsoft Windows 是美国微软公司(Microsoft)于 1983 年开始研发的以图形用户界面为基础的操作系统，是全球应用最广泛的操作系统。

Windows 1.0 版本于 1985 年 11 月 20 日 推出，1990 年 5 月 22 日，Windows 3.0 正式发布后开始取得商业上的巨大成功，1995年发布的 Windows95 更是划时代的版本。特别说明的是，Windows 3.2 只有简体中文版，但意义非凡——给硬软汉字库之争划上时代的句号，基本上彻底埋葬了以汉卡为卖点的中国众多公司：联想、巨人、金山、方正、浪潮......

## Q/A

### 相对路径快捷方式
```
 将目标修改为 explorer.exe ..\..\dir1\file1
```

### 有些文件/目录无法删除
```
 目前发现用阿里云盘在 windows 10/11 下载会出现此种现象，需要在 cmd 环境下，用 dir /x 查看短文件名(8.3)删除
```

### 路由
```
 # 192.168.0.* -> 192.168.0.1, -p 表示永久路由
 route -p add 192.168.0.0 mask 255.255.255.0 192.168.0.1
 route delete 192.168.0.0
```

### PowerShell

#### Alias
 ```

# 1. 检查配置文件是否存在
Test-Path $PROFILE

# 2. 如果不存在则创建

# 实际上是在当前用户下创建了：Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1 文件。
New-Item -Type File -Force $PROFILE

# 3. 编辑配置文件
notepad $PROFILE

# 4. 在配置文件中添加别名
function ll { Get-ChildItem -Force @args }                      # ls -l
function la { Get-ChildItem -Force -Hidden @args }              # ls 只显示隐藏文件
```

#### SSH 连接超时

在当前用户目录 .ssh 下创建 .ssh 目录，及文件 Config
```
 ServerAliveInterval 60
```

### 远程桌面连接出现“要求的函数不受支持”
```
 目标机器配置“远程桌面”时，去掉默认选中的“仅允许运行使用网络级别身份验证的远程桌面的计算机连接”
 P.S. 打开 regedit，增加什么 CredSSP 的，均为胡扯。
```

### 硬件不符合要求安装 Windows 11
```
 删除安装 U 盘 Sources 目录中的 appraiserres.dll 文件。安装系统时会提示硬件不符合要求，强制升级产生问题不负责的提醒，忽略直接继续安装即可。
```

### Windows 11 无法切换 拼音/五笔
```
 只能切换中英文，无法切换中文输入法。将输入法“删除”再“添加键盘”即可
```

### Windows 11 彻底关闭快速访问文件夹
```
 资源管理器 -> 菜单栏"…" -> "选项" -> "隐私"
```

### 目录挂载

目录联接：使用目录联接把数据重定向到别的目录
```
 mklink /J C:\ProgramData\test D:\mount\test
```

挂载卷：将单独的物理卷挂载到目录
```
 磁盘管理（diskmgmt.msc）
 创建一个新分区或虚拟磁盘，不要给它分配盘符
 右键该卷 → 更改驱动器号和路径 → 添加 → 选择 C:\ProgramData\test
```

目录联接与快捷方式区别

| 比较项 | 目录联接 | 快捷方式 | 备注 |
|:---|:---|:---|:---|
| + |
| 类型 | NTFS 文件系统级别 | 用户界面级别 |
| 使用方式 | 系统、程序、命令行都认为它是真实目录 | 只能由资源管理器打开 |
| 命令行  cd 进入 | 是 | 否 | 本质是 `.lnk` 文件 |
| 直接读写 | 透明读写 | 程序会读到的是 .lnk 文件 |
| 资源管理器中点开 | 是 | 是（但只是“跳转”） |
| 作用对象 | 完全替代真实目录 | 仅用于快速访问某个路径 |
| 作为系统目录替代 | 可用于迁移系统路径 | 否 |

### Windows 11 删除分区

有些分区只能在命令行下删除，如 EFI 分区。

**diskpart**
```
 list disk                 # 显示磁盘或分区(partition)，选中者前面会有 * 号
 select disk               # 选择磁盘或分区(partition)
 delete partition override # 删除所选分区（立即生效）
```

### 远程登录

微软近年来强化了 Windows Hello（PIN码/生物识别）的安全性，系统默认会拦截纯密码的远程登录。
```
 设置 -> 账户 -> 登录选项
 关闭：仅允许此设备上的 Microsoft 账户使用 Windows Hello 登录
```

### Windows 共享文件夹（SMB）
1. 打开 控制面板 → 程序和功能 → 启用或关闭 Windows 功能

勾选 SMB 1.0/CIFS 文件共享支持（建议勾 SMB Direct 支持，性能更好）。
2. 选择一个文件夹 → 右键 → 属性 → 共享 → 高级共享

权限：给指定用户或“Everyone”读写权限（根据需要）。

### 移动热点

设置 -> 网络和 Internet -> 网络和 Internet -> 移动热点
- 编辑：网络属性：WiFi 名称/密码
- 关闭：节能
- 连接的设备，最多八台。

### PowerToys

PowerToys 是微软官方推出的 Windows 实用工具集，用于增强 Windows 操作系统的生产力和用户体验，是 Windows 官方增强工具箱。

如可以将 CapsLock 键设置为中英文切换快捷键，也可以发现快捷键冲突。
1. 安装：在 Microsoft Store 中搜索 PowerToys 安装
1. 映射：键盘管理器 -> 重新映射键 -> 添加新映射

### HTML 无法复制
```
  - Console
 document.body.contentEditable='true'
```
 
```
 或者禁掉 Java Script
 （Chrome）设置 -> 隐私与安全 -> 网站设置 -> 不允许使用 JavaScript（添加）
```

### 服务无法停掉

Win+R，输入 msconfig，选择引导选项卡，勾选安全引导，重启电脑。进入安全模式。

### Windows 11 无法识别 USB 设备
```
 版本未做任何限制，可向下兼容。可能的原因是线的问题，有些 USB 可以充电，但无法使用数据传输功能。
```

### Windows 2022 Server

#### 改变磁盘分区大小

settings --> system --> Storage --> Manage Disks and Volumes --> (选中磁盘)Properties --> Change size

#### 设置虚拟内存

settings --> About --> Advanced system settings --> settings

## BUG

### tree

在 PowerShell 中，进入一个不存在的目录，执行 tree /F 会出现很多文件滚屏。（Windows 11 企业版 24H2 - 26100.6899）
