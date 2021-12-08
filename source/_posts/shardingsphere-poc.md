---
title: shardingsphere poc
date: 2021-12-08 15:43:47
tags: database
---

### 搭建postgres环境
分库我们就简单在本地postgres instance创建两个db来测试，分表是fw，fw2
![postgres-two-db](/images/postgres-two-db.png)
创建一个叫xuhang的schema
```
jdbc:postgresql://localhost:5433/fw?currentSchema=xuhang
jdbc:postgresql://localhost:5433/fw2?currentSchema=xuhang
```

在fw 数据库的xuhang schema里面都执行下postgresql-fw-xuhang.sql
在fw2数据库的xuhang2 schema里面都执行下postgresql-fw2-xuhang2.sql
因为：
t_order这个表做的是分表的策略，他始终都是在ds0，所以order表就fw数据库创建就够了
t_position这个表示做的分库分表的策略，而且都是自定义的，分表是根据日期字段，分库是根据id对2取模，需要在两个数据库都创建

### 测试
https://gitee.com/mengxu2018/shardingsphere-test

OrderMapperTest
这个会创建四个order，设置4个id，order_id % 4 根据这个来决定到fw数据库的哪个表

PositionMapperTest
会根据position id来决定哪个db，日期来决定哪个table

### next step
可以研究下怎么用另外client 表的table_prefix字段来决定其他业务表怎么插入，每个业务表都有一个client的字段，
比如model_generation_a,model_generation_b,model_generation_c,model_generation_d
插入的时候，根据client id去client表查询table prefix，然后插入对应的model_generation_x表