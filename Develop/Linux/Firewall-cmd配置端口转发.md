---
source_title: Firewall-cmd配置端口转发
categories:
- Develop
- Linux
last_modified: '2023-12-18T01:16:57Z'
---
### firewall status

systemctl status firewalld.service

### 重新加载防火墙

firewall-cmd --reload

### Rule List

firewall-cmd --list-all

### 设置

#### 检查是否允许伪装IP

firewall-cmd --query-masquerade
- firewall-cmd --add-masquerade --permanent
- --add-masquerade  # 允许防火墙伪装IP
- --remove-masquerade# 禁止防火墙伪装IP

#### 永久生效
- --permanent 永久生效，否则重启/reload失效

#### 开放端口
- firewall-cmd --zone=public --add-port=32000-32099/tcp
- firewall-cmd --zone=public --add-port=33000-33099/udp
- firewall-cmd --zone=public --remove-port=32000-32099/tcp

#### 转发
- firewall-cmd --add-forward-port=port=32010:proto=tcp:toport=3389:toaddr=193.122.104.2
- firewall-cmd --remove-forward-port=port=32010:proto=tcp:toport=3389:toaddr=193.122.104.2

#### 批量添加端口映射
```
 ## fw.sh
 #!/bin/sh
```
 
```
 if [ "$1" == "add" ]; then
     OT="add"
 elif [ "$1" == "remove" ]; then
     OT="remove"
 else
     echo "Not Parameter."
     exit 1
 fi
```
 
```
 if [ "$2" == "" ]; then
     LN_NAME="fw.txt"
 else
     LN_NAME=$2
 fi
```
 
```
 cat ${LN_NAME} | while read LN
 do
     LN=`echo ${LN} |awk -F"#" '{print \$1}'`
     if [ "${LN}" == "" ]; then
         echo ${OT} ${LN}
     else
         echo ${OT} ${LN}
         firewall-cmd --${OT}-forward-port=${LN}
         firewall-cmd --${OT}-forward-port=${LN} --permanent
     fi
 done
```

### Example
```
 ## fw.txt
 # win-remote
 port=32010:proto=tcp:toport=3389:toaddr=192.168.33.2 
 # SSH 22
 port=32023:proto=tcp:toport=22:toaddr=192.168.33.3
 # other
 port=33010:proto=tcp:toport=5432:toaddr=192.168.33.4
 port=33011:proto=tcp:toport=9200:toaddr=192.168.33.28
 port=33020:proto=tcp:toport=8123:toaddr=192.168.33.36
 port=33021:proto=tcp:toport=9000:toaddr=192.168.33.36
 # web
 port=33080:proto=tcp:toport=80:toaddr=192.168.33.4
```

## 参考
1. [防火墙富规则、内部上网](https://www.cnblogs.com/gongjingyun123--/p/12018442.html)
1. [CloudFlare Tunnel 免费内网穿透的简明教程](https://sspai.com/post/79278)
