---
source_title: Cloudflare setup
categories:
- Doc
- Help
last_modified: '2025-07-10T08:18:48Z'
---

### 内网穿透

Zero Trust -> Networks -> Tunnels
```
 Zero Trust -> Access -> Tunnels(2023 old)
```

#### Create a tunnel
- Name your tunnel TEST
- Choose your environment
- Install and run a connector
```
 ## 内网主机
 ## Linux: https://github.com/cloudflare/cloudflared/releases :  cloudflared-fips-linux-amd64
 #   mv cloudflared-fips-linux-amd64 /usr/bin/cloudflared
 #   chmod 755 /usr/bin/cloudflared
 ## Centos/Redhat: https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-x86_64.rpm
 #   yum localinstall cloudflared.rpm
 cloudflared service install 
```

每个 tunnel 均需要一个 token，如果已经建立过需要更新，那么先删除，再安装：
```
 cloudflared service uninstall
```

创建 tunnel，还会在相应的域名 DNS 下创建 CNAME 记录，若删除 tunnel，需要同步删除相应的 DNS 解析。

#### Public hostnames

##### HTTP
- Add public hostname for http://localhost:8088
```
 p88.server.com --> http://127.0.0.1:8088
```

##### SSH
- Add public hostname for localhost:22
```
 ssh.server.com --> ssh://127.0.0.1:22
```
- Client
```
 # /usr/local/bin/cloudflared
 .ssh/config
 Host ssh.server.com
 ProxyCommand /usr/local/bin/cloudflared access ssh --hostname %h
```
 
```
 ssh ssh.server.com
```

### DNS
1. 在域名提供商 nameserver 中指向 cloudflare(carrera.ns.cloudflare.com, matteo.ns.cloudflare.com)
1. 在 cloudflare 建立 DNS 解析，如：A 类解析 mwbbs.eu.org 132.145.61.120。如果某些地区无法正确解析，可以选择：不通过 proxy 处理

### 设置访问规则

可使用 Cloudflare 安全规则阻止 GPTBot。

网站域名 -> Security -> Security rules -> Create rules
```
 Rule name(required); User Agent(使用代理者程式); contains(包含); GPTBot
 Action: Block
 # 支持使用 or 匹配多个字符串
```

**测试**
```
 curl -A "GPTBot/1.2; +https://openai.com/gptbot" https://mwbbs.eu.org
```
