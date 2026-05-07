---
source_title: PHP执行shell
categories:
- Develop
- Php
- Web
last_modified: '2023-01-06T02:34:34Z'
---
  - PHP - 执行shell**

### php
```
 <?php
```

     exec("/var/www/html/killpidginf.sh");
```
 ?>
```

### SHELL
```
 #!/bin/sh
 ssh root@g-inf.mwbbs.cf "netstat -lntp |grep ':32022' |grep 'tcp '|awk '{print \$7}'|awk -F'/' '{print \$1}'|xargs kill -9"
 echo 0
```

### 权限
```
 执行远程机器上的 shell，需要：
 * 在 /var/www/.ssh 放入 id_rsa 及 known_hosts，所有者设置为当前 web 执行者，如：www-data:www-data
```

### Example
- index.html
```
 
 
 
 
 <p style="font-size:80px">&nbsp;&nbsp;Menu:</p>
 <p style="font-size:24px">&nbsp;&nbsp;没事别乱按下面链接，除非特别需要。</p>
 <p style="font-size:80px">
```

    <span>&nbsp;&nbsp;&nbsp;&nbsp;1. <a href="killpidginf.php">restart M202 tunnel</a></span>
```
 </p>
 
```
- killpidginf.php
```
 <!DOCTYPE html>
 
 
 <?php
```

     exec("/var/www/html/mwbbs/killpidginf.sh");

     echo " <p style='font-size:80px'>&nbsp;&nbsp;Result:</p>";

     echo "<p style=\"font-size:40px\">";

     echo "   &nbsp;&nbsp;&nbsp;&nbsp;Success! Wait a minute and try connecting again.";

     echo "</p>";
```
 ?>
 
 
```
- killpidginf.sh
```
 #!/bin/sh
 ssh root@g-inf.mwbbs.cf "netstat -lntp |grep ':32022' |grep 'tcp '|awk '{print \$7}'|awk -F'/' '{print \$1}'|xargs kill -9"
 echo `date "+%Y-%m-%d %H:%M:%S"` restart g-inf ssh tunnel. >> log_mwbbs.log
 echo 0
```
