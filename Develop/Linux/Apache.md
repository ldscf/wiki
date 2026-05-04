---
source_title: Apache
categories:
- Develop
- Linux
last_modified: '2024-12-06T02:05:16Z'
---
Centos7 系统自带的 Apache 安装
```
 yum install httpd
 systemctl start httpd
 # systemctl enable httpd
```

### 配置
```
 vi /etc/httpd/conf/httpd.conf
```
```
 Alias /dev "/u01/Dev"
 
     Options Indexes MultiViews FollowSymLinks
     IndexOptions +Charset=utf-8
     Allow from all
     Order allow,deny
     AllowOverride All
     Require all granted
 
```

### Error

#### AH00686: cannot read directory for multi

SELinux 未关闭造成
```
 chown -R www-data:www-data $PATH
 -.OR.-
 setenforce 0
```
