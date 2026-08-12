---
source_title: Python template
categories:
- Develop
- Python
last_modified: '2023-12-29T05:27:02Z'
---
Python Template Sample

#### Python Template
```
 #!/usr/bin/python3
 # -*- coding: utf-8 -*-
 #
 import upy
 HELP = 
 ------------------------------------------------------------------------------
```

   Name     : pangoin - INF

   Purpose  : ETL

   Author   : Adam
 
   Revisions:

   Ver        Date        Author           Description

   ---------  ----------  ---------------  ------------------------------------

   1.0        2020/11/15  Adam             Create
 
```
 format:
```

     app = pet/pet2/pup/pup2
```
 ------------------------------------------------------------------------------
```
 
 
```
 def pangoin(d_in):
```

     import sys

     import usys
 
     d_val = {'type':'pangolin', 'app':'pet', 'time_b':None, 'time_e':None, 'secs':0, 'result':0, 'message':None, 'memo':None}
 
     # get dict from command parameter

     d_cp_tmp = usys.cp(1, HELP)
 
     s_tmp = d_cp_info.get('app', *).lower()*

     if not s_tmp:

         print(HELP)

         sys.exit(1)
 
     return d_val
 
 
```
 if __name__ == "__main__":
```

     d_res = pangoin()

     print(d_res)

     rs = ucgi.ulog(d_res)
