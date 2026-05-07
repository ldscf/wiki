---
source_title: PhpBB3
categories:
- Develop
- Platform
- Web
last_modified: '2025-12-29T02:33:57Z'
---
![Phpbb.png](https://mwbbs.eu.org/wiki/images/e/e2/Phpbb.png)

About phpBB^®^

Millions of people use phpBB on a daily basis, making it the most widely used open source bulletin board system in the world. Whether you want to stay in touch with a small group of friends or are looking to set up a large multi-category board for a corporate website, phpBB has the features you need built-in.

### SEO

phpbb3(3.3.5)

#### 页面的标头部分缺少说明
```
 
```
   
```
 
```
- Bing
```
 
```
- mwbbs
```
 
```

#### title
```
 牛奶河BBS - Milky Way BBS
```

### phpbb3 安装

#### PHP
```
 # Ubuntu 20.04
 apt install libapache2-mod-php7.4 openssl php-imagick php7.4-common php7.4-curl php7.4-gd php7.4-imap php7.4-intl php7.4-json php7.4-ldap php7.4-mbstring php7.4-mysql php7.4-pgsql php-ssh2 php7.4-sqlite3 php7.4-xml php7.4-zip php-net-ftp unzip
```
 
```
 # Ubuntu 22.04
 apt install openssl php-imagick php-ssh2 php-net-ftp unzip php8.1 php8.1-sqlite3 php8.1-cli php8.1-common php8.1-curl php8.1-gd php8.1-imap php8.1-intl php8.1-mbstring php8.1-mysql php8.1-xml php8.1-zip
```

#### MySQL
```
 # apt update
 # apt install mysql-server
 CREATE DATABASE phpbb DEFAULT CHARACTER SET UTF8;
 CREATE USER 'phpbbuser'@'%' IDENTIFIED BY 'Bi.3';
 GRANT ALL PRIVILEGES ON phpbb.* TO 'phpbbuser'@'%';
```

![Phpbb3_email.png](https://mwbbs.eu.org/wiki/images/1/11/Phpbb3_email.png)

#### Email
- SMTP 服务器地址和协议：smtp-mail.outlook.com
- SMTP 服务器端口：587
- SMTP 验证方式：PLAIN
- SMTP 用户名：mwbbs2022@hotmail.com
- SMTP 密码：MWw.123
- 验证 SSL 证书：是
- 验证 SMTP 端名字：是

#### LOGO
```
 /var/www/html/phpbb/styles/prosilver/theme/images/site_logo.svg
```

#### Head
```
 \styles\prosilver\template\
```

   overall_header.html

   simple_header.html
 
```
 
 
```

#### 配置等级

否则只有一个管理员等级，很令人迷惑

用户等级_1.ranks下有相应图标（支持子目录）

用户等级_2.用户和组 -> 管理等级

用户等级_3.等级表单

用户等级_4.等级表单_指派特殊等级

维护 -> 错误日志

#### connect db config
```
 config.php
```

#### Using Redis

提升论坛在高负载下的性能。需要注意的是：此配置下的打包备份，在无 redis 环境使用，需要改 config 并清除缓存（文件缓存：cache/production/，模板缓存 cache/production/twig）。
```
 # install
 apt install redis
```
 
```
 apt install php-pear
 apt install php-dev
 # apt install php-redis
 pecl install redis
```
 
```
 # setup
 # config.php
 //$acm_type = 'phpbb\\cache\\driver\\file';
 $acm_type = 'phpbb\\cache\\driver\\redis';
```
 
```
 # 在 phpbb3 中清空缓存，重启 Apache2
```

### phpbb3 恢复
```
 mysql phpbb << ${FILENAME}.sql
 可能需要清除缓存：ACP -> 综合 -> 清除缓存
```

### phpbb3 升级
```
 *OLD: current
 *NEW: phpBB3
```

#### path
```
 #上传的附件
 cp -par files ../phpBB3/
 # 等级、表情
 cp -par images ../phpBB3/
 # 自定义
 cp -par mwbbs ../phpBB3/
 # 聊天窗口
 cp -par ext/dmzx ../phpBB3/ext/
```

#### file
```
 cp ${FN} ../phpBB3/.
```
- config.php
- sitemap.xml
- robots.txt
- 各搜索引擎的证书/验证文件
- styles/prosilver/theme/images/site_logo.svg

文件修改
- styles/prosilver/template/overall_header.html
- styles/prosilver/template/simple_header.html
```
 牛奶河BBS - Milky Way BBS
 
```
```
 chown -R www-data:www-data ../phpBB3
```

#### phpbb_upgrade.sh
 ```
#!/bin/bash

# 当前目录
CURRENT=/var/www/html
NEW=/var/www/phpBB3
cd $CURRENT
#上传的附件
cp -par ${CURRENT}/files ${NEW}/

# 等级、表情
cp -par ${CURRENT}/images ${NEW}/

# 自定义
cp -par ${CURRENT}/mwbbs ${NEW}/
FL_I=(
   index.html
   config.php
   sitemap.xml
   googleba45e31fd4a15ff5.html
   sitemap.xml
   robots.txt
   styles/prosilver/theme/images/site_logo.svg
   #下面两个文件最好手动修改
   #styles/prosilver/template/overall_header.html
   #styles/prosilver/template/simple_header.html
)
for FN in ${FL_I[*]};do
   cp -pa ${CURRENT}/${FN} ${NEW}/${FN}
done
chown -R www-data:www-data ${NEW}
```

### phpbb3 常用表

#### 版主列表
```
 select * from phpbb_moderator_cache;
```

#### 修改查看次数

original backup
```
 create table phpbb_topics_bak as select * from phpbb_topics;
```

ID, title, views
```
 select   topic_id, topic_title, topic_views
 from     phpbb_topics_bak
 order by topic_id;
```

update
```
 update   phpbb_topics
 set      topic_views = 1
 where    topic_id = 10;
```
 
```
 UPDATE phpbb_topics AS t JOIN phpbb_topics_bak AS s 
 ON     s.topic_id = t.topic_id
 SET    t.topic_views = s.topic_views;
```

### BOT

#### Google Bot
```
 # Googlebot
 66.249.64.18 - - [21/Feb/2022:21:44:59 +0800] "GET /styles/prosilver/theme/zh_cmn_hans/stylesheet.css?assets_version=2 HTTP/1.1" 200 444 "http://www.ldscf.cf/" "Mozilla/5.0 (Linux; Android 6.0.1; Nexus 5X Build/MMB29P) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Mobile Safari/537.36 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
```

#### Bing
```
 # bingbot/
 192.168.32.3 - - [21/Feb/2022:02:57:04 +0800] "GET /phpbb/index.php HTTP/1.1" 200 4045 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)"
```

#### Archive
```
 # archive.org_bot
 207.241.233.139 - - [17/Sep/2022:16:55:47 +0800] "GET /viewtopic.php?t=426&sid=120e04ab709103432a26b7f66986d239 HTTP/1.0" 200 5574 "-" "Mozilla/5.0 (compatible; archive.org_bot +http://archive.org/details/archive.org_bot)"
```

#### petalsearch
```
 # 据说是华为的
 114.119.160.84 - - [04/Jan/2023:15:09:01 +0800] "GET /viewtopic.php?t=455&view=print HTTP/1.1" 200 1774 "http://132.145.63.24/viewtopic.php?t=455" "Mozilla/5.0 (Linux; Android 7.0;) AppleWebKit/537.36 (KHTML, like Gecko) Mobile Safari/537.36 (compatible; PetalBot;+https://webmaster.petalsearch.com/site/petalbot)"
```

#### DuckDuckGo
```
 69.64.55.90 - - [10/Apr/2023:19:05:36 +0800] "GET / HTTP/1.1" 200 3886 "http://www.mwbbs.tk" "DuckDuckBot/1.0; (+http://duckduckgo.com/duckduckbot.html)"
```

### Q

#### 登录失效

可能的原因如下：
- 会话过期时间 (Session Length): General -> Server configuration -> Security settings -> Session length
- 会话 IP 验证 (Validate session IP address): Security settings -> Validate session IP address。若 IP 地址经常变化（使用动态 IP 或代理），将导致会话失效
- 服务器时间问题
