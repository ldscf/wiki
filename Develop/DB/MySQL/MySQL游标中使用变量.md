---
source_title: MySQL游标中使用变量
categories:
- DB
- Develop
- MySQL
last_modified: '2022-12-30T07:57:44Z'
---
MySQL 游标中使用变量
- 使用临时表存储变量，游标中使用SQL取出来变量，达到值不断变化的目的

#### 两级游标，使用变量

CREATE PROCEDURE psmm.p_cursor_test4() BEGIN
```
   DECLARE flag int;
   DECLARE cs int;
   DECLARE rs VARCHAR(128);
   DECLARE tn VARCHAR(128);
   DECLARE v_schema VARCHAR(128);
   DECLARE v_sql VARCHAR(4000);
   DECLARE v_col VARCHAR(128);
   DECLARE v_col_list VARCHAR(4000);
```
   
```
   DECLARE gflag int;
   DECLARE i int;
```
   
```
   DECLARE cur_tab     CURSOR FOR SELECT table_rows,table_name FROM information_schema.`TABLES` where TABLE_SCHEMA = f_cursor_var('schema');
   DECLARE cur_tab_col CURSOR FOR select column_name from information_schema.columns where table_schema = f_cursor_var('schema') and table_name = f_cursor_var('table') order by  ordinal_position;
   declare continue handler for not found set flag = 1;
```
   
```
   drop TEMPORARY table if exists s_cursor_var;
   CREATE TEMPORARY TABLE s_cursor_var (
      ky varchar(8), val varchar(96)
   );
```
   
   
```
   set i = 1;
   set gflag = 0;
   while gflag <> 1 do
      truncate table s_cursor_var;
      insert into s_cursor_var select ky, val from s_cursor_temporary where id = i;
      commit;
```
      
```
      # get table
      set cs = 0;
      set flag = 0;
      set v_col_list = *;*
      OPEN cur_tab;
      FETCH cur_tab INTO rs, tn;
      while flag <> 1 DO
         set v_col_list = concat(tn, ' : ');
         # get col list
         delete from s_cursor_var where ky = 'table';
         insert into s_cursor_var values ('table', tn);
         commit;
         OPEN cur_tab_col;
         set flag = 0;
         fetch cur_tab_col into v_col;
         while  flag <> 1 DO
            set v_col_list = concat(v_col_list, v_col);
            set v_col_list = concat(v_col_list, ', ');
            fetch cur_tab_col into v_col;
         end while;
         CLOSE cur_tab_col;
         select v_col_list;
```
```
         set flag = 0;
         FETCH cur_tab INTO rs, tn;
      end while;
      CLOSE cur_tab;
```
      
```
      set i = i + 1;
      select i;
      if i > 3 then
         set gflag = 1;
      end if;
```
   
```
   end while;
```

END

#### 按 key 取表数据
```
 create function psmm.f_cursor_var (
    s_ky                      VARCHAR(8)
 )
    RETURNS                   VARCHAR(96)
    READS SQL DATA
 BEGIN
```
 
```
    DECLARE EXIT HANDLER FOR SQLWARNING, NOT FOUND, SQLEXCEPTION BEGIN
       RETURN 'Not Found.';
    END;
```
    
```
    DECLARE s_val             VARCHAR(96);
```
 
```
    select   val
    into     s_val
    from     s_cursor_var
    where    ky = s_ky
    ;
```
 
```
    RETURN s_val;
```
 
```
 END
```

#### 临时表 s_cursor_temporary
```
 CREATE TABLE s_cursor_temporary (
    ky varchar(8), val varchar(96), id int,
    primary key (ky)
 );
```
 
```
 insert into s_cursor_temporary ('schema', 'ods', 1);
 insert into s_cursor_temporary ('schema', 'psmm', 1);
 insert into s_cursor_temporary ('schema', 'hdfs', 1);
```
