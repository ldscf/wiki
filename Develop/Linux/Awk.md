---
source_title: Awk
categories:
- Develop
- Linux
- Shell
last_modified: '2023-05-16T12:33:58Z'
---
### filter

#### 包含字符串，如root

ps -ef|sed '1d' |awk /^root/'{++SUM[$1]}END {for(c in SUM) print c, SUM[c]}'

#### 不含字符串，如root

ps -ef|sed '1d' |awk '{++SUM[$1]}END {for(c in SUM) print c, SUM[c]}' | prep -v 'root'

#### 多个字符串

| awk '/broadcast|Bcast/'
| grep -E 'broadcast|Bcast' OR egrep 'broadcast|Bcast'

### sub

匹配指定域/记录中最大、最靠左边的子字符串的正则表达式，并用替换字符串替换这些字符串

sub 每行只匹配一次，gsub 匹配多次

awk?'{?gsub(/test/,?"mytest", $1);?print?$0; }'?testfile

awk?-F'|'?-v?OFS='|'?'{?gsub(/[0-9]/,?"",?$3);?print?$0;?}'?data.txt

# [/x30-/x39]/ = [0-9]

### 分类汇总

#### 将 Web 访问记录按访问 IP 汇总
```
 cat /var/log/apache2/access.log | grep petalsearch | awk '{++SUM[$1]}END {for(c in SUM) print c, SUM[c]}' | sort -n
```
 
```
 114.119.159.19 1
 114.119.162.62 1
 132.145.61.120 19
```

# 系统日志

cat messages|grep 'May 5'|awk '{print $3}' |awk -F':' '{++SUM[$1]}END {for(c in SUM) print c, SUM[c]}'|sort -n

cat messages|grep 'May 5 14'

#

# 20190515144501;2;0;0;6831244;31620;633456;0;0;84808;1032;5215;6919;32;11;39;18;0

cat vmstat.log |grep ^'20190515'|cut -c 9-10|awk '{++SUM[$1]}END {for(c in SUM) print c, SUM[c]}'|sort -n

cat vmstat.log |grep ^'2019051514'

#httpd log

#按选定日期IP访问排名，去掉 static=资源目录，etl_web_refresh=auto refresh

DATEID='05/Jul/2019'

cat access_log |sed 's/ - - /--/g' |sed 's/ "POST /--/g' |sed 's/ "GET /--/g' |sed 's/ HTTP\/1.1" 200 /--/g' |sed 's/"/--/g' |grep -v '/static/' |grep -v 'etl_web_refresh' |sed 's/\/cgi-bin\///g' |awk -F'--' '{print $1, $3, $2, $4}' |grep ${DATEID}| awk '{++SUM[$1]}END {for(c in SUM) print c, SUM[c]}' | sort -k2nr
