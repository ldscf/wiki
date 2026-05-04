---
source_title: Greenplum Export CSV
categories:
- DB
- Develop
- Greenplum
last_modified: '2023-01-18T14:45:04Z'
---
Greenplum Export CSV

### Greenplum Export CSV
```
 # m02, bi, ~/tmp/
 PW=Bi...1123
 FN=/tmp/test_cross.txt
```

#### Table
```
 SSQL="logs.l_serv_monitor"
```

#### SQL
```
 SSQL=`cat test_cross.sql`
```
 
```
 # test_cross.sql
 select   create_date,
          platform,
          operatingsystemversion,
          channelid,
          channelname,
          count(*) cs
 from     ods.gio_ads_track_activation
 where    1=1
 and      date_hour_id like '202210%'
 group by create_date,
          platform,
          operatingsystemversion,
          channelid,
          channelname
 order by create_date,
          platform,
          operatingsystemversion,
          channelid,
          channelname
```

### Export
```
 PGPASSWORD=${PW} psql -U gpadmin -h 10.10.139.20 -d owgp -w -c "copy (${SSQL}) to STDOUT with (FORMAT csv, header true, encoding UTF8)" > ${FN}
```

### Res: test_cross.txt
```
 2022-10-01,Android,Android 10,"","",1752
 2022-10-01,Android,Android 10,a9BG6rRn,wap站跳转app,7
 2022-10-01,Android,Android 10,baidufeeds,百度原生信息流,83
 2022-10-01,Android,Android 10,huaweiads_app_ocpd,华为应用商店oCPD,1143
```
