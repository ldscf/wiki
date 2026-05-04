---
source_title: Java DB
categories:
- Develop
- Java
last_modified: '2024-02-26T02:05:15Z'
---
### Public
```
 // register JDBC Driver
 Class.forName(sDrive);
 conn = DriverManager.getConnection(sJdbc, sUser, sPasswd);
 curr = conn.createStatement();
 // execute sql and return result set
 rs = curr.executeQuery(sSQL);
```
 
```
 rs.getMetaData().getColumnCount();
 rs.getMetaData().getColumnName(j)
 obj1 = rs.getObject(j);
 lcol1.add(obj1 == null?null:obj1.toString());  // ArrayList lcol1 = new ArrayList<>();
```

| + |
| rowspan="4" | rs | rowspan="3" | getMetaData | getColumnCount | 结果集列数量 |
| getColumnName | 列名称 |
| getColumnType | 列数据类型 |
| getObject | 列值 |
```
 // execute sql and not return, ddl/insert/delete/...
 curr.execute(sSQL);
```

### MySQL
```
 // sDrive = "com.mysql.jdbc.Driver";
 // MySQL 8.0 以上版本 - JDBC 驱动名及数据库 URL
 sDrive = "com.mysql.cj.jdbc.Driver";
 sJdbc = "jdbc:mysql://%s:%s/%s?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC";
 String sHost, sPort, sDB, sUser, sPasswd;
 sHost = dbinfo.get("host");
 sPort = dbinfo.get("port");
 sDB = dbinfo.get("db");
 sUser = dbinfo.get("user");
 sPasswd = dbinfo.get("passwd");
```
