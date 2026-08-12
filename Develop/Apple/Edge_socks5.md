---
source_title: Edge socks5
categories:
- Apple
- Develop
- Windows
last_modified: '2026-01-21T05:49:05Z'
---
Microsoft Edge 是由微软开发的一款基于 Chromium 内核（与 Google Chrome 同源）的浏览器。Edge 在 2020 年进行了彻底重构，为 Windows 系统的默认浏览器，也是全球最流行的浏览器之一。可以使用所有 Chrome 应用商店里的扩展插件。

使用本地 SOCKS5 代理，通常有两种方式：一种是使用系统自带的设置（全局生效），另一种是使用浏览器插件（局部）。

### 安装插件
1. 打开 Edge 浏览器，访问 [Microsoft Edge 加载项商店](地址栏右侧，扩展 -> 获取 Microsoft Edge 扩展)
1. 搜索并安装 Proxy SwitchyOmega
1. 在 Proxy SwitchyOmega 中“新建情景模式”
  1. 代理协议选择 SOCKS5
  1. 代理服务器：127.0.0.1
  1. 代理端口：1080

### 创建本地 SOCKS 代理服务器
```
 ssh -C -f -N -D 1080 user@abc.com
 # 测试
 curl -x socks5://127.0.0.1:1080 https://ifconfig.me
```

参考 [SSH 隧道 - 动态端口转发](https://<!-- template: SERVERNAME -->/wiki/index.php/SSH_%E9%9A%A7%E9%81%93#%E5%8A%A8%E6%80%81%E7%AB%AF%E5%8F%A3%E8%BD%AC%E5%8F%91%EF%BC%88-D%EF%BC%89)
