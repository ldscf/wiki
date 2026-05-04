---
source_title: Centos多IP
categories:
- Develop
- Linux
last_modified: '2024-02-01T09:09:32Z'
---
DHCP 获取 IP 的配置 /etc/sysconfig/network-scripts/ifcfg-ens192
```
 TYPE="Ethernet"
 PROXY_METHOD="none"
 BROWSER_ONLY="no"
 BOOTPROTO="dhcp"
 DEFROUTE="yes"
 IPV4_FAILURE_FATAL="no"
 IPV6INIT="yes"
 IPV6_AUTOCONF="yes"
 IPV6_DEFROUTE="yes"
 IPV6_FAILURE_FATAL="no"
 IPV6_ADDR_GEN_MODE="stable-privacy"
 NAME="ens192"
 UUID="f051563c-d6b2-4068-ac17-256b76ec8d14"
 DEVICE="ens192"
 ONBOOT="yes"
```

加上如下配置后，会获得两个 IP
```
 IPADDR="192.168.0.181"
 PREFIX="24"
 GATEWAY="192.168.0.1"
 DNS1="192.168.0.1"
 IPV6_PRIVACY="no"
```

systemctl restart network

ip addr
```
 ens192:  mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:8e:dc:be brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.182/24 brd 192.168.0.255 scope global noprefixroute dynamic ens192
       valid_lft 86397sec preferred_lft 86397sec
    inet 192.168.0.181/24 brd 192.168.0.255 scope global secondary noprefixroute ens192
       valid_lft forever preferred_lft forever
    inet6 fe80::d731:d09:95d4:867b/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

此时，如果想保留静态 IP，注释掉如下一行即可
```
 #BOOTPROTO="dhcp"
```
