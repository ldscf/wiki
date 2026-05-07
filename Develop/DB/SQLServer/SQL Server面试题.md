---
source_title: SQL Server面试题
categories:
- DB
- Develop
- SQLServer
last_modified: '2023-11-25T10:18:59Z'
---
### 统计销售最差的车

车辆按批销售，每次销售若干辆同型号的车，表中就记录增加一条记录。

问：查询总销售量大于100，且总销售辆最少的3个型号的车及其总销售量。

| MODEL | CNT |
| A | 20 |
| B | 50 |
| B | 100 |
| C | 900 |
| C | 500 |
| D | 400 |
| E | 200 |
| F | 40 |
| G | 300 |
```
 /* Q1: Temp Table 车俩销售日志
```
  * 若含有中文数值，改为 nvarchar

  *
  * 2023/8/8 Adam

  */
```
 create table #tmp_salelog (
```

    d_model   varchar(8),

    v_cnt     int
```
 )
 ;
```
```
 insert into #tmp_salelog values
 ('A', 20),
 ('B', 50),
 ('B', 100),
 ('C', 900),
 ('C', 500),
 ('D', 400),
 ('E', 200),
 ('F', 40),
 ('G', 300)
 ;
```
```
 -- 问：查询总销售量大于100，且总销售辆最少的3个型号的车及其总销售量
 select   top 3
```

          d_model, sum(v_cnt) v_cnt_total
```
 from     #tmp_salelog
 group by d_model
 having   sum(v_cnt) > 100
 order by v_cnt_total
 ;
```

### 游戏打包销售折扣

销售平台进行游戏打包促销。将任意个游戏打包为一组，根据游戏数量制定折扣。打包的游戏数量限定2个至4个。当包含2个游戏时折扣为9折，3个时8折，4个时6折。

问1：计算有多少种(个数)不同的打包组合方式

问2：打包购买游戏时，分别计算出2个打包，3个打包和4个打包时价格最贵的包。要求最终用一个查询得出结果
```
 /* Q2: Temp Table 游戏价格
```
  * 若含有中文数值，改为 nvarchar
  * 2023/8/8 Adam

  */
```
 create table #tmp_games (
```

    d_gname   varchar(8),

    v_amt     numeric(8,2)
```
 )
 ;
```
```
 insert into #tmp_games values
 ('A', 29.33),
 ('B', 19.22),
 ('C', 25.81),
 ('D', 16.79),
 ('E', 20.78),
 ('F', 25.32)
 ;
```
```
 /*
```
  * tmp_games 中数据，关于 gname 应该唯一。否则需要预警及去重

  */
```
 -- 问1：计算有多少种(个数)不同的打包组合方式
 -- 如果游戏比较多（大于 300 时，本写法产生的笛卡尔积将达到三千万），需要另外一种的写法：通过排列组合计算
```
 
```
 -- 答案一：允许打包重复游戏
 select   '2' type_id, count(*) num
 from     #tmp_games t1, #tmp_games t2
 where    t1.d_gname <= t2.d_gname
 union all
 select   '3' type_id, count(*) num
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3
 where    t1.d_gname <= t2.d_gname
 and      t2.d_gname <= t3.d_gname
 union all
 select   '4' type_id, count(*) num
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3, #tmp_games t4
 where    t1.d_gname <= t2.d_gname
 and      t2.d_gname <= t3.d_gname
 and      t3.d_gname <= t4.d_gname
 ;
```
 
```
 -- 答案二：不允许打包重复游戏
 select   '2' type_id, count(*) num
 from     #tmp_games t1, #tmp_games t2
 where    t1.d_gname < t2.d_gname
 union all
 select   '3' type_id, count(*) num
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3
 where    t1.d_gname < t2.d_gname
 and      t2.d_gname < t3.d_gname
 union all
 select   '4' type_id, count(*) num
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3, #tmp_games t4
 where    t1.d_gname < t2.d_gname
 and      t2.d_gname < t3.d_gname
 and      t3.d_gname < t4.d_gname
 ;
```
 
 
```
 -- 问2：打包购买游戏时，分别计算出2个打包，3个打包和4个打包时价格最贵的包。要求最终用一个查询得出结果
 -- 答案一：允许打包重复游戏
 select   *
 from     (
 select   top 1
```

          '2' type_id,

          t1.d_gname + t2.d_gname d_game_group,

          Convert(decimal(18,2), (t1.v_amt + t2.v_amt)*0.9) amt_total
```
 from     #tmp_games t1, #tmp_games t2
 where    t1.d_gname <= t2.d_gname
 order by 3 desc
 )a
 union all
 select   *
 from     (
 select   top 1
```

          '3' type_id,

          t1.d_gname + t2.d_gname + t3.d_gname d_game_group,

          Convert(decimal(18,2), (t1.v_amt + t2.v_amt + t3.v_amt)*0.8) amt_total
```
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3
 where    t1.d_gname <= t2.d_gname
 and      t2.d_gname <= t3.d_gname
 order by 3 desc
 )b
 union all
 select   *
 from     (
 select   top 1
```

          '4' type_id,

          t1.d_gname + t2.d_gname + t3.d_gname + t4.d_gname d_game_group,

          Convert(decimal(18,2), (t1.v_amt + t2.v_amt + t3.v_amt + t4.v_amt)*0.6) amt_total
```
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3, #tmp_games t4
 where    t1.d_gname <= t2.d_gname
 and      t2.d_gname <= t3.d_gname
 and      t3.d_gname <= t4.d_gname
 order by 3 desc
 )c
```
 
 
 
 
```
 -- 答案二：不允许打包重复游戏
 select   *
 from     (
 select   top 1
```

          '2' type_id,

          t1.d_gname + t2.d_gname d_game_group,

          Convert(decimal(18,2), (t1.v_amt + t2.v_amt)*0.9) amt_total
```
 from     #tmp_games t1, #tmp_games t2
 where    t1.d_gname < t2.d_gname
 order by 3 desc
 )a
 union all
 select   *
 from     (
 select   top 1
```

          '3' type_id,

          t1.d_gname + t2.d_gname + t3.d_gname d_game_group,

          Convert(decimal(18,2), (t1.v_amt + t2.v_amt + t3.v_amt)*0.8) amt_total
```
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3
 where    t1.d_gname < t2.d_gname
 and      t2.d_gname < t3.d_gname
 order by 3 desc
 )b
 union all
 select   *
 from     (
 select   top 1
```

          '4' type_id,

          t1.d_gname + t2.d_gname + t3.d_gname + t4.d_gname d_game_group,

          Convert(decimal(18,2), (t1.v_amt + t2.v_amt + t3.v_amt + t4.v_amt)*0.6) amt_total
```
 from     #tmp_games t1, #tmp_games t2, #tmp_games t3, #tmp_games t4
 where    t1.d_gname < t2.d_gname
 and      t2.d_gname < t3.d_gname
 and      t3.d_gname < t4.d_gname
 order by 3 desc
 )c
```

### 经销商代理品牌额度统计

DEALER_INFO 表在每个经销商申请代理一个品牌的额度时，单独维护对应的基本信息

CREDIT_LIMIT_INFO 表维护每个授信协议的额度金额

问：要求以相同的证件号码为唯一标识识别为一个客户，取每个客户最近一次维护的基本信息进行报送，同时取这个客户已用额度总和。

| DEALER_INFO |
| 经销商授信协议号码 | 经销商名称 | 经销商证件号 | 注册地址 | 员工人数 | 信息维护日期 |
| DEALER_NUMBER | DEALER_NAME | DEALER_ID_NO | ADDRESS | STUFF_NUMBER | MODIFY_DATE |
| 1001001 | TEST_01 | ID_001 | BEIJING CHAOYANG | 100 | 2020-01-01 |
| 2001001 | TEST_01 | ID_001 | BEIJING CHAOYANG | 200 | 2020-02-01 |
| 1002002 | TEST_02 | ID_002 | SHANGHAI PUDONG | 1000 | 2020-03-15 |
| 3002002 | TEST_02 | ID_002 | SHANGHAI MINHANG | 1000 | 2020-01-20 |
| 2003003 | TEST_03 | ID_003 | BEIJING HAIDIAN | 50 | 2020-02-25 |
| 4003003 | TEST_03 | ID_003 | BEIJING DONGCHENG | 200 | 2020-01-01 |
| 1004004 | TEST_04 | ID_004 | SHANGHAI HUANGPU | 100 | 2020-03-01 |
| CREDIT_LIMIT_INFO |
| 经销商授信协议号码 | 品牌 | 已用额度 |
| DEALER_NUMBER | BRAND | UTILIZED_LIMIT |
| 1001001 | FAW | 1000 |
| 2001001 | SKODA | 1500 |
| 1002002 | FAW | 200 |
| 3002002 | AUDI | 400 |
| 2003003 | SVW | 500 |
| 4003003 | PORCHE | 50 |
| 1004004 | FAW | 700 |
最终报文数据样式

| 客户名称 | 证件号码 | 注册地址 | 员工人数 | 已用额度 |
| DEALER_NAME | DEALER_ID_NO | ADDRESS | STUFF_NUMBER | UTILIZED_LIMIT |
| TEST_01 | ID_001 | BEIJING CHAOYANG | 200 | 2500 |
| TEST_02 | ID_002 | SHANGHAI PUDONG | 1000 | 600 |
| TEST_03 | ID_003 | BEIJING HAIDIAN | 50 | 550 |
| TEST_04 | ID_004 | SHANGHAI HUANGPU | 100 | 700 |
```
 /* Q3: Temp Table 经销商信息
```
  * 若含有中文数值，改为 nvarchar
  * 2023/8/8 Adam

  */
```
 create table #tmp_dealer_info (
```

    d_dealer_number    varchar(8),

    d_dealer_name      varchar(100),

    d_dealer_id_no     varchar(8),

    d_address          varchar(200),

    v_stuff_number     INT,

    d_modify_date      date
```
 )
 ;
```
 
```
 insert into #tmp_dealer_info
 values
 ('1001001','TEST_01','ID_001','BEIJING CHAOYANG',100,'2020-01-01'),
 ('2001001','TEST_01','ID_001','BEIJING CHAOYANG',200,'2020-02-01'),
 ('1002002','TEST_02','ID_002','SHANGHAI PUDONG',1000,'2020-03-15'),
 ('3002002','TEST_02','ID_002','SHANGHAI MINHANG',1000,'2020-01-20'),
 ('2003003','TEST_03','ID_003','BEIJING HAIDIAN',50,'2020-02-25'),
 ('4003003','TEST_03','ID_003','BEIJING DONGCHENG',200,'2020-01-01'),
 ('1004004','TEST_04','ID_004','SHANGHAI HUANGPU',100,'2020-03-01')
 ;
```
 
```
 create table #tmp_credit_limit_info (
```

    d_dealer_number    varchar(8),

    d_brand            varchar(100),

    v_utilized_limit   INT
```
 )
 ;
```
 
```
 insert into #tmp_credit_limit_info
 values
 ('1001001','FAW',1000),
 ('2001001','SKODA',1500),
 ('1002002','FAW',200),
 ('3002002','AUDI',400),
 ('2003003','SVW',500),
 ('4003003','PORCHE',50),
 ('1004004','FAW',700)
```
```
 -- 问：要求以相同的证件号码为唯一标识识别为一个客户，取每个客户最近一次维护的基本信息进行报送，同时取这个客户已用额度总和。
 -- 证件号码为唯一标识，为两表关联条件、分组排序条件
 -- P.S. 有可能出现""协议号码""未使用额度（无记录），显示为零
 select   --
```

          o1.d_dealer_name,

          o1.d_dealer_id_no,

          o1.d_address,

          o1.v_stuff_number,

          o2.v_utilized_limit_total
```
 from     (
```

          -- 按经销商证件号分组，倒排信息维护日期, rn=1 为最后修改记录

          select   d_dealer_number,

                   d_dealer_name,

                   d_dealer_id_no,

                   d_address,

                   v_stuff_number,

                   d_modify_date,

                   row_number() over(partition BY d_dealer_id_no order BY d_modify_date desc) rn

          from     #tmp_dealer_info

          ) o1,

         (

         -- 客户已用额度总和，若未使用额度（无记录），显示为零

         select   t1.d_dealer_id_no,

                  isnull(sum(t2.v_utilized_limit), 0)  v_utilized_limit_total

         from     #tmp_dealer_info t1

         left join #tmp_credit_limit_info t2 on t1.d_dealer_number = t2.d_dealer_number

         group by t1.d_dealer_id_no

         ) o2
```
 where    o1.d_dealer_id_no = o2.d_dealer_id_no
 and      o1.rn = 1
 ;
```
