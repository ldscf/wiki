---
source_title: Python CGI test
categories:
- Develop
- Python
last_modified: '2022-12-29T07:40:58Z'
---
Python CGI - get post json

#### test_cgi
```
 #!/usr/bin/python3
 # -*- coding: utf-8 -*-
 print("Content-type: text/html")
 print("")
 #
 import upy
```
 
```
 import os
 import time
 import ucgi
 import ustr
 import usys
```
 
 
```
 try:
     form = ucgi.uform()['CGI']
     #d_form = form['CGI']
```
 
```
 except Exception as e:
     print(e)
     sys.exit(1)
```
 
```
 print(form)
```
 
```
 #d_form = ustr.udict(form)
```
 
```
 for key, value in form.items():
     if value[:8] == '%BASE64%':
         form[key] = usys.base64(value)
```
 
```
 print(form)
```

#### Example:

##### Get
```
 curl "http://10.10.137.16/cgi-bin/test_cgi?user=admin&passwd=12345678"
 {'passwd': '12345678', 'user': 'admin'}
 {'passwd': '12345678', 'user': 'admin'}
```
 

##### Post
```
 curl -d "user=admin&passwd=12345678" http://10.10.137.16/cgi-bin/test_cgi
 {'user': 'admin', 'passwd': '12345678'}
 {'user': 'admin', 'passwd': '12345678'}
```
 

##### Post Json
```
 T1="{'user': 'admin', 'passwd': '12345678',  'sql':'%BASE64%c2VsZWN0ICogZnJvbSBsX2V0bCB3aGVyZSBkYXRlX2lkID0gdG9fY2hhcihzeXNkYXRlLCAneXl5eW1tZGQnKQo='}"
 URL="http://10.10.137.16/cgi-bin/test_cgi"
 curl -H "Content-Type:application/json" -X POST -d "$T1" $URL
```
 
```
 {'user': 'admin', 'passwd': '12345678', 'sql':  '%BASE64%c2VsZWN0ICogZnJvbSBsX2V0bCB3aGVyZSBkYXRlX2lkID0gdG9fY2hhcihzeXNkYXRlLCAneXl5eW1tZGQnKQo='}
 {'user': 'admin', 'passwd': '12345678', 'sql': "select * from l_etl where date_id = to_char(sysdate, 'yyyymmdd')\n"}
```
