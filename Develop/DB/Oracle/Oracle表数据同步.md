---
source_title: Oracle表数据同步
categories:
- DB
- Develop
- Oracle
last_modified: '2024-09-29T03:24:38Z'
---
-- 这段代码有3好，不好的地方也不想改了

create or replace procedure p_tab_for_ogg

as

v_cols         varchar2(4000);

v_sql          varchar2(10000);

type           t_cursor    is ref cursor;

c_cursor       t_cursor;

v_id      number;

tab_record          tab%rowtype;

tab_for_ogg_record  tab%rowtype;

v_set_col      varchar2(10000);

begin
```
  select listagg(column_name, ', ') within group (order by column_id) into v_cols
```
```
  from  user_tab_cols t
```
```
  where t.table_name=upper('tab');
```
```
  -- a minus b  1 insert
```
```
  insert into tab_for_ogg
```
```
    select * from tab
```
```
    where id in (select id from tab
```
```
                      minus
```
```
                      select id from tab_for_ogg);
```
```
  commit;
```
```
  -- b minus a  0 delete
```
```
  delete from tab_for_ogg
```
```
    where id in (select id from tab_for_ogg
```
```
                      minus
```
```
                      select id from tab);
```
```
  commit;
```
```
  -- a intersect b  2
```
```
  v_sql :='(select id from tab intersect select id from tab_for_ogg)
```
```
             minus
```
```
            (select id from (select '||v_cols||' from tab
```
```
                                    intersect
```
```
                                  select '||v_cols||' from tab_for_ogg))
```
```
                                  ';
```
```
  --dbms_output.put_line(v_sql);
```
```
  open c_cursor for v_sql;
```
```
  loop
```
```
     fetch c_cursor into v_id;
```
```
     exit when c_cursor%notfound;
```
```
     select * into tab_record from tab
```
```
     where id=v_id;
```
```
     select * into tab_for_ogg_record from tab_for_ogg
```
```
     where id=v_id;
```
```
     --字段( 不支持变量 )
```
```
    /* for c in (select column_name from  user_tab_cols t where t.table_name=upper('tab'))
```
```
     loop
```
```
         --dbms_output.put_line(c.column_name);
```
```
         v_str := 'tab_record.%c%';
```
```
         v_str := replace(v_str,'%c%',c.column_name);
```
```
         execute immediate v_str into v1;
```
```
         dbms_output.put_line(v1);
```
```
     end loop;*/
```
```
     v_set_col := '';
```
```
     --字段
```
```
     if nvl(tab_record.id,0) != nvl(tab_for_ogg_record.id,0) then
```
```
        v_set_col := v_set_col||'id,';
```
```
     end if;
```
```
     if nvl(tab_record.COMP_ID,0) != nvl(tab_for_ogg_record.COMP_ID,0) then
```
```
        v_set_col := v_set_col||'COMP_ID,';
```
```
     end if;
```
```
     if nvl(tab_record.NAME,0) != nvl(tab_for_ogg_record.NAME,0) then
```
```
        v_set_col := v_set_col||'NAME,';
```
```
     end if;
```
```
     if nvl(tab_record.DESCA,0) != nvl(tab_for_ogg_record.DESCA,0) then
```
```
        v_set_col := v_set_col||'DESCA,';
```
```
     end if;
```
```
     if nvl(tab_record.PARENT_id,0) != nvl(tab_for_ogg_record.PARENT_id,0) then
```
```
        v_set_col := v_set_col||'PARENT_id,';
```
```
     end if;
```
```
     if nvl(tab_record.PARENT_idS,0) != nvl(tab_for_ogg_record.PARENT_idS,0) then
```
```
        v_set_col := v_set_col||'PARENT_idS,';
```
```
     end if;
```
```
     if nvl(tab_record.PARENT_NAMES,0) != nvl(tab_for_ogg_record.PARENT_NAMES,0) then
```
```
        v_set_col := v_set_col||'PARENT_NAMES,';
```
```
     end if;
```
```
     if nvl(tab_record.DTYPE,0) != nvl(tab_for_ogg_record.DTYPE,0) then
```
```
       v_set_col := v_set_col||'DTYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.DEPT_STYPE,0) != nvl(tab_for_ogg_record.DEPT_STYPE,0) then
```
```
        v_set_col := v_set_col||'DEPT_STYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.BTYPE,0) != nvl(tab_for_ogg_record.BTYPE,0) then
```
```
        v_set_col := v_set_col||'BTYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.SUB,0) != nvl(tab_for_ogg_record.SUB,0) then
```
```
        v_set_col := v_set_col||'SUB,';
```
```
     end if;
```
```
     if nvl(tab_record.PSTORE,0) != nvl(tab_for_ogg_record.PSTORE,0) then
```
```
        v_set_col := v_set_col||'PSTORE,';
```
```
     end if;
```
```
     if nvl(tab_record.MUSID,0) != nvl(tab_for_ogg_record.MUSID,0) then
```
```
        v_set_col := v_set_col||'MUSID,';
```
```
     end if;
```
```
     if nvl(tab_record.MPSID,0) != nvl(tab_for_ogg_record.MPSID,0) then
```
```
        v_set_col := v_set_col||'MPSID,';
```
```
     end if;
```
```
     if nvl(tab_record.CD,0) != nvl(tab_for_ogg_record.CD,0) then
```
```
        v_set_col := v_set_col||'CD,';
```
```
     end if;
```
```
     if nvl(tab_record.DE_TYPE,0) != nvl(tab_for_ogg_record.DE_TYPE,0) then
```
```
        v_set_col := v_set_col||'DE_TYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.EDT,sysdate) != nvl(tab_for_ogg_record.EDT,sysdate) then
```
```
        v_set_col := v_set_col||'EDT,';
```
```
     end if;
```
```
     if nvl(tab_record.STATUS,0) != nvl(tab_for_ogg_record.STATUS,0) then
```
```
        v_set_col := v_set_col||'STATUS,';
```
```
     end if;
```
```
     if nvl(tab_record.CT,sysdate) != nvl(tab_for_ogg_record.CT,sysdate) then
```
```
        v_set_col := v_set_col||'CT,';
```
```
     end if;
```
```
     if nvl(tab_record.UT,sysdate) != nvl(tab_for_ogg_record.UT,sysdate) then
```
```
        v_set_col := v_set_col||'UT,';
```
```
     end if;
```
```
     if nvl(tab_record.HR_DID,0) != nvl(tab_for_ogg_record.HR_DID,0) then
```
```
        v_set_col := v_set_col||'HR_DID,';
```
```
     end if;
```
```
     if nvl(tab_record.COMPANY,0) != nvl(tab_for_ogg_record.COMPANY,0) then
```
```
        v_set_col := v_set_col||'COMPANY,';
```
```
     end if;
```
```
     if nvl(tab_record.STYPE,0) != nvl(tab_for_ogg_record.STYPE,0) then
```
```
        v_set_col := v_set_col||'STYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.IS_VTD,0) != nvl(tab_for_ogg_record.IS_VTD,0) then
```
```
        v_set_col := v_set_col||'IS_VTD,';
```
```
     end if;
```
```
     if nvl(tab_record.DB_TYPE,0) != nvl(tab_for_ogg_record.DB_TYPE,0) then
```
```
        v_set_col := v_set_col||'DB_TYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.DBSUB_TYPE,0) != nvl(tab_for_ogg_record.DBSUB_TYPE,0) then
```
```
        v_set_col := v_set_col||'DBSUB_TYPE,';
```
```
     end if;
```
```
     if nvl(tab_record.UQC,0) != nvl(tab_for_ogg_record.UQC,0) then
```
```
        v_set_col := v_set_col||'UQC,';
```
```
     end if;
```
```
     if nvl(tab_record.LEAF,0) != nvl(tab_for_ogg_record.LEAF,0) then
```
```
        v_set_col := v_set_col||'LEAF,';
```
```
     end if;
```
```
     if nvl(tab_record.MD,0) != nvl(tab_for_ogg_record.MD,0) then
```
```
        v_set_col := v_set_col||'MD,';
```
```
     end if;
```
```
     if nvl(tab_record.SOURCE,0) != nvl(tab_for_ogg_record.SOURCE,0) then
```
```
        v_set_col := v_set_col||'SOURCE,';
```
```
     end if;
```
```
     if nvl(tab_record.STORE,0) != nvl(tab_for_ogg_record.STORE,0) then
```
```
        v_set_col := v_set_col||'STORE,';
```
```
     end if;
```
```
     if nvl(tab_record.PRE_PId,0) != nvl(tab_for_ogg_record.PRE_PId,0) then
```
```
        v_set_col := v_set_col||'PRE_PId,';
```
```
     end if;
```
```
     if nvl(tab_record.PRE_PIdS,0) != nvl(tab_for_ogg_record.PRE_PIdS,0) then
```
```
        v_set_col := v_set_col||'PRE_PIdS,';
```
```
     end if;
```
```
     if nvl(tab_record.PRE_PNAMES,0) != nvl(tab_for_ogg_record.PRE_PNAMES,0) then
```
```
        v_set_col := v_set_col||'PRE_PNAMES,';
```
```
     end if;
```
```
     v_set_col := trim(',' from v_set_col);
```
```
     --update
```
```
     if v_set_col is not null then
```
```
       v_sql:='update tab_for_ogg set ('||v_set_col||')=(select '||v_set_col||' from tab where id='||v_id||') where id='||v_id;
```
```
       execute immediate v_sql;
```
```
       commit;
```
```
     end if;
```
```
   end loop;
```
```
   close c_cursor;
```

exception

when others then
```
    rollback;
```

end;
