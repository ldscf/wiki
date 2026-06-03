---
source_title: Wireguard
categories:
- Develop
- Linux
- Platform
last_modified: '2026-04-06T04:26:06Z'
---
WireGuard 是一种实现加密虚拟专用网络（VPN）的通信协议和免费开源软件，通过 UDP 传递流量，旨在比 IPsec 和 OpenVPN 这两种常见的隧道协议具有更好的性能和更强大的功能，其设计目标是易于使用、高速性能和低攻击面。

### 服务端

#### sysctl
```
 # /etc/sysctl.conf
 net.ipv4.ip_forward = 1
```

#### wireguard
```
 apt install openresolv
 apt install wireguard
```

#### 生成秘钥对
```
 wg genkey | tee server_privatekey | wg pubkey > server_publickey
 wg genkey | tee u01_privatekey | wg pubkey > u01_publickey
 wg genkey | tee u02_privatekey | wg pubkey > u02_publickey
```

#### 服务配置
 ```

# Server: /etc/wireguard/wg0.conf
[Interface]
PrivateKey = ${server_privatekey}
Address = 20.0.0.1/32
ListenPort = 23189
DNS = 1.1.1.1
MTU = 1420
PostUp   = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE; iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1300
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -D FORWARD -o wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE; iptables -t mangle -D FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1300
[Peer]
PublicKey = ${u01_publickey}
AllowedIPs = 20.0.0.10/32
[Peer]
PublicKey = ${u02_publickey}
AllowedIPs = 20.0.0.20/32
```

P.S. 
- Address = 20.0.0.1/32 表示将 IP 地址 20.0.0.1 分配给 WireGuard 接口，并且该接口只有一个可用的 IP 地址。 这对于 WireGuard 的点对点连接（peer-to-peer）模式来说是标准配置，简化了路由，避免了 IP 地址冲突，并保证了接口之间的隔离。这种配置方式非常适合于 VPN 连接，因为每个连接通常只需要一个独立的 IP 地址。
- ens3 是网卡名称，可以用 ip addr 看到

#### 连接配置
 ```

# Client: /etc/wireguard/u01.conf
[Interface]
PrivateKey = ${u01_privatekey}
Address = 20.0.0.10/32
DNS = 8.8.8.8
MTU = 1420
[Peer]
PublicKey = ${server_publickey}
Endpoint = ${server_ip}:23189
AllowedIPs = 0.0.0.0/0, ::0/0
PersistentKeepalive = 25
```

客户端导入此文件即可。

#### 启动
```
 wg-quick up wg0
 # shutdown
 wg-quick down wg0
 # status
 wg
```
```
 # 查看 MSS 钳制转发链，pkts > 0，有多少个 TCP 包被强制“剪短”
 #   -> iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1300
 iptables -t mangle -L FORWARD -v
```

#### 配置启动服务（可选）
 ```

# systemctl start wireguard

# /etc/init.d/wireguard
#!/bin/bash

### BEGIN INIT INFO

# Provides:		wgstart

# Required-Start:	$remote_fs $syslog

# Required-Stop:    $remote_fs $syslog

# Default-Start:	2 3 4 5

# Default-Stop:		0 1 6

# Short-Description:	wireguard

### END INIT INFO
wg-quick up wg0
chmod +x /etc/init.d/wireguard
cd /etc/init.d
update-rc.d wireguard defaults
```

### 客户端

[Windows 下载](https://www.wireguard.com/install/)（Mac/IOS 从 App Store 下载） wireguard，扫描 **[二维码](../../Article/科学/二维码)** 或导入从服务端创建的客户端文件即可。

Ubuntu 安装：
```
 apt install wireguard
 # 将客户端文件 u31.conf 拷贝至 /etc/wireguard
 wg-quick up u31
```

若服务端修改了端口，客户端在配置中只需要修改端口重起即可。

### 修改端口

#### 定时修改服务端口
 ```

# mc2.kr:/etc/sync/wgmodify.sh
#!/bin/bash
config_file="/etc/sync/wg0.conf"

# 随机端口范围
port_min=10000
port_max=62000

# 生成随机端口
generate_random_port() {
  local min=$1
  local max=$2
  echo $(($min + RANDOM % ($max - $min + 1)))
}
modify_listen_port() {
  local new_port=$(generate_random_port "$port_min" "$port_max")
  sed -i "s/ListenPort = [0-9]*/ListenPort = $new_port/" "$config_file"
  echo "ListenPort -> $new_port"
}

# exist wg0.conf
if [ ! -f "$config_file" ]; then
  echo "错误: 配置文件 '$config_file' 不存在"
  exit 1
fi

# Modify wg0.conf's ListenPort
modify_listen_port
exit 0
```

#### 通过网页修改服务端口
1. 通过 php，修改 wg0.conf(调整文件权限或归属)
1. 如果无法直接修改 wg0.conf 文件，如：wireguard 与 web 分属两台主机。则可以通过 shell 将文件同步，并重启服务
 ```

# /var/www/html/wg.php
<?php
$file_path = '/etc/sync/wg0.conf';
$key = 'ListenPort = ';
$error = '';
$message = '';
$listen_port = '';
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (isset($_POST['listenPort']) && is_numeric($_POST['listenPort'])) {
        $new_port = intval($_POST['listenPort']);
        try {
            $content = file_get_contents($file_path);
            if ($content === false) {
                throw new Exception('Failed to read the file.');
            }
            $pattern = '/' . preg_quote($key, '/') . '\d+/';
            $replacement = $key . $new_port;
            $updated_content = preg_replace($pattern, $replacement, $content);
            if ($updated_content === null) {
                throw new Exception('Failed to update the file content.');
            }
            if (file_put_contents($file_path, $updated_content) === false) {
                throw new Exception('Failed to write to the file.');
            }
            $message = 'File updated successfully!';
        } catch (Exception $e) {
            $error = $e->getMessage();
        }
    } else {
        $error = 'Invalid ListenPort value.';
    }
}
try {
    $content = file_get_contents($file_path);
    if ($content === false) {
        throw new Exception('Failed to read the file.');
    }
    $pattern = '/' . preg_quote($key, '/') . '(\d+)/';
    if (preg_match($pattern, $content, $matches)) {
        $listen_port = $matches[1];
    } else {
        throw new Exception('ListenPort not found in the file.');
    }
} catch (Exception $e) {
    $error = $e->getMessage();
}
?>
<!DOCTYPE html>
    
    
    Edit ListenPort
    Modify ListenPort</h1>
    <?php if ($error): ?>
        <?php echo htmlspecialchars($error); ?>
    <?php endif; ?>
    <?php if ($message): ?>
        <?php echo htmlspecialchars($message); ?>
    <?php endif; ?>
    
        ListenPort:
        " required>
        Submit
    
```
 ```
#!/bin/bash
export PATH=$PATH:/sbin:/usr/sbin
TIMEID=`date '+%Y/%m/%d %H:%M:%S'`
src="root@mc3:/etc/sync/wg0.conf"
dest="/etc/wireguard/wg0.conf"
rsync -a $src $dest
v1=`md5sum ${dest} | awk -F' ' '{print $1}'`
v2=`md5sum ${dest}.bak | awk -F' ' '{print $1}'`
if [ "$v1" == "$v2" ];then
    echo $TIMEID 'The file has not changed.'
else
    echo $TIMEID 'The file has changed, restart wg0.'
    cp $dest ${dest}.bak
    cd /etc/wireguard
    wg-quick down wg0
    wg-quick up wg0
fi
```
```
 # Shell in wireguard server(Local)
```

#### 修改客户端连接端口

一般客户端与 Wireguard 不在一个网段，所以需要先判断网络连通性。若发现变更，则从 Wireguard 服务端同步 wg0.conf，重启服务。
 ```
#!/bin/bash
CONF_CLIENT="u33"
WG_SVR=user@wg_server_ip
export PATH=$PATH:/sbin:/usr/sbin
TIMEID=$(date '+%Y/%m/%d %H:%M:%S')
res=$(wg)
if [ "${res}X" == "X" ]; then
    echo "$TIMEID The wg has not exist."
else
    res=$(ping -c 3 8.8.8.8 2>&1 | grep "received" | awk '{if ($6 ~ /%/) print $6; else print "N/A"}')
    if [ "${res}" == "0%" ]; then
        echo "$TIMEID The wg has not changed."
        exit 0
    fi
    wg-quick down "$CONF_CLIENT"
fi
src="${WG_SVR}:/etc/wireguard/wg0.conf"
dest="/etc/wireguard/wg0.conf"
rsync -a "$src" "$dest"
cd /etc/wireguard
PORT=$(grep ListenPort /etc/wireguard/wg0.conf | awk '{print $3}')
PORT="$WG_SVR:$PORT"
CONF_FILE="${CONF_CLIENT}.conf"
PORT_OLD=$(grep Endpoint "$CONF_FILE" | awk '{print $3}')
if [ -n "$PORT_OLD" ]; then
    sed -i "s|$PORT_OLD|$PORT|g" "$CONF_FILE"
    echo "$TIMEID The wg has changed, restart $CONF_CLIENT."
fi
wg-quick up "$CONF_CLIENT"
exit 0
```

### Error

#### Warning: ?? is world accessible

This means that the configuration file permissions are too broad - and they shouldn’t, as there’s a private key in there. This can be fixed with
```
 chmod 600 /etc/wireguard/wg0.conf
```

#### resolvconf: command not found
```
 /usr/bin/wg-quick: line 32: resolvconf: command not found
 1. apt install openresolv
 2. 如果是在 shell 里执行命令，一般在 .bash_profile, .bashrc 等文件中的环境变量不会带进来，需要执行 source ~/.bashrc 等命令。或者：
```

    export PATH=$PATH:/sbin:/usr/sbin   # resolvconf 在 /usr/sbin
