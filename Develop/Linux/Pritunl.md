---
source_title: Pritunl
categories:
- Develop
- Linux
last_modified: '2024-07-11T06:12:18Z'
---
Pritunl supports all OpenVPN clients and has official clients for several devices and platforms.
- [Pritunl](https://pritunl.com/) 
- [Github: pritunl](https://github.com/pritunl/pritunl)

### Install Pritunl

#### Step 1: Update your system
```
 apt update
 # apt -y upgrade
```

#### Step 2: Add Pritunl and MongoDB repositories and public keys
```
 echo "deb http://repo.pritunl.com/stable/apt focal main" | tee /etc/apt/sources.list.d/ pritunl.list
 echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4  multiverse" | tee /etc/apt/sources.list.d/mongodb-org-4.4.list
 curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
 # apt-key adv --keyserver hkp://keyserver.ubuntu.com --recv  9DA31620334BD75D9DCB49F368818C72E52529D4
 apt-key adv --keyserver hkp://keyserver.ubuntu.com --recv  7568D9BB55FF9E5287D586017AE645C0CF8E292A
```
 
```
 apt update
```

#### Step 3: Install Pritunl and MongoDB
```
 apt --assume-yes install pritunl mongodb-server
 systemctl start pritunl mongodb
 systemctl enable pritunl mongodb
```

#### Step 4: Setup Pritunl
```
 pritunl setup-key
 7425bf593de640edb6c14f934e4d7961
```
 
```
 # https://IP:433
 输入上面的 KEY
```
 
```
 # 在执行下面的语句之前，若没有输入 KEY，则会出现如下错误：
 # pymongo.errors.ConfigurationError: Empty host (or extra comma in host list).
 pritunl default-password
```
 
```
 # reset passwd
 pritunl reset-password
```

#### Other
```
 # 443 --> 30443,  禁用 80 web
 pritunl set app.server_port 30443.       # 也可以在页面右上 settings 改
 pritunl set app.redirect_server false    # 禁用 80 web
```

### Client Route
```
 openvpn --daemon --cd /etc/openvpn/client --config BI_m105_BI.ovpn --log-append ~/log/ openvpn.log --auth-nocache
 ip route add 10.0.0.0/8 via 10.10.105.47 dev ens160
```
 
```
 # 需要鉴权
 openvpn --daemon --cd /u01/vpn --log-append /u01/vpn/openvpn.log --config $1 --auth-user-pass vpnpass.txt
 ip route add 221.0.0.0/8 via 192.168.32.1 dev enp0s6
 # route del -net 221.0.0.0 netmask 255.0.0.0
 # cat vpnpass.txt 
 vpn
 vpn
```
