---
source_title: Linux硬连接
categories:
- Develop
- Linux
last_modified: '2023-01-19T12:17:23Z'
---
Linux - 硬连接
- format: ln [-s] source target
- -s 为软连接，支持目录
- 相当于做了副本。如果文件修改，则同步，删除则副本数 -1
- 有些工具通过 sftp 编辑后，操作上会先删除后建，而不是修改文件，造成相关文件未修改。如：sublime(2021,v4121) + transmit(v5.8.2).
  - vi OK
  - WinSCP(5.1.5) + UlartEdit-32(13.20) OK.

### ls -li
- 第一列是文件的 inum，第三列是文件的原本+副本个数
```
 total 60
```

    957266 -rw-r--r--. 1 oracle oinstall   137 Apr  8 11:36 autopart.ini

    957265 -rwxr--r--. 1 oracle oinstall 20930 Apr  8 10:48 crypt

      3131 -rwxr-xr-x. 1 oracle oinstall  1171 Apr  8 10:48 dbct

   1972103 -rw-r--r--. 1 oracle oinstall  2557 Apr 25 10:24 db.ini

  69504907 drwxr-xr-x. 2 oracle oinstall    51 Apr 15 15:03 del

      3968 -rwxr-xr-x. 1 oracle oinstall  1241 Apr  8 10:48 pangolin

   1640003 -rwxr-xr-x. 1 oracle oinstall   257 Apr 25 10:29 pangolin_vppdb_g.sh

   1750678 -rwxr-xr-x. 1 oracle oinstall   336 Apr 25 10:28 pangolin_vppdb.sh
```
 100663423 drwxr-xr-x. 4 oracle oinstall   175 Apr 23 13:51 pet
```

  33787555 drwxr-xr-x. 3 oracle oinstall   130 Apr 23 13:53 ppy

      4009 -rw-r--r--. 1 oracle oinstall   466 Apr  8 10:48 readme.txt

      3998 -rw-r--r--. 1 oracle oinstall   727 Apr  8 10:48 upy.pyc
```
 104915311 -rw-r--r--. 2 oracle oinstall  3527 Apr 30 11:17 usql_sys
```

### 查询 inum 文件位置

find / -inum 104915311 2>/dev/null
