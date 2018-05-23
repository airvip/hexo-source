---
title: Mysql 把数据按照时间段统计
date: 2018-01-04 12:22:37
tags:
    - Mysql
---

> 枯燥的太阳因你而变的温暖，脆弱的心灵因你而变的坚强。

因项目需求，要把数据按照时间段进行统计，现在把在 Mysql 经常使用的数据统计 sql 记录一下，方便之后查看学习。

<!-- more -->

# 表的数据结构
![统计数据表内容](/img/201801/mysqltable/sqltable01.png)

说明：
create_time 与 update_time 为 int 类型
表名：question_log

# 常用SQL统计

按年统计
``` bash
SELECT 
  COUNT(1) AS num,FROM_UNIXTIME(create_time,'%Y') AS t
FROM
  questions_log
GROUP BY
  t;
```

按月统计某年的数据， '2017'为变量数据
``` bash
SELECT 
  COUNT(1) AS num,FROM_UNIXTIME(create_time,'%Y-%m') AS t
FROM
  questions_log
WHERE
  FROM_UNIXTIME(create_time,'%Y') = '2017'
GROUP BY
  t;
```

按日统计某月的数据， '2017-12'为变量数据
``` bash
SELECT 
  COUNT(1) AS num,FROM_UNIXTIME(create_time,'%Y-%m-%d') AS t
FROM
  questions_log
WHERE
  FROM_UNIXTIME(create_time,'%Y-%m') = '2017-12'
GROUP BY
  t;
```

按小时统计某天的数据，'2017-12-25'为变量数据
``` bash
SELECT 
  COUNT(1) AS num,FROM_UNIXTIME(create_time,'%H') AS t
FROM
  questions_log
WHERE
  FROM_UNIXTIME(create_time,'%Y-%m-%d') = '2017-12-25'
GROUP BY
  t;
```

按每 20 分钟统计某天的数据，'2017-12-25'为变量数据
``` bash
SELECT 
  COUNT(1) AS num ,CONCAT(FROM_UNIXTIME(create_time,'%H:'),
  FLOOR(FROM_UNIXTIME(create_time,'%i')/20)*20) AS t
FROM 
  questions_log
WHERE 
  FROM_UNIXTIME(create_time,'%Y-%m-%d') = '2017-12-25'
ORDER BY 
  t ;
```

# 所用相关函数说明

floor()：函数只返回整数部分，小数部分舍弃。（向下取整）
``` bash
select floor(1.1);#结果1
select floor(1.9);#结果1
```

ceil()：函数只返回将小数部分进位后的整数。（向上取整）
``` bash
select ceil(1.1);#结果2
select ceil(1.9);#结果2
```

round()：函数四舍五入，大于0.5的部分进位，不到则舍弃。（四舍五入）
``` bash
mysql> select round(1.1);#结果1
mysql> select round(1.9);#结果2
mysql> select round(5.12,1);#结果5.1
```

curdate()：获取当天日期。
``` bash
mysql> select curdate();#2018-01-04
```

curtime()：获取现在时间。
``` bash
mysql> select curdate();#14:20:00
```

now():获取现在时间
``` bash
select now();#2018-01-04 14:37:44
```

week():获取周几，从0开始
year():获取年
month():获取月
day():获取日
hour():获取时
minute():获取分
second():获取秒
``` bash
mysql> select week('2018-01-17');#0
mysql> select year('2018-01-17');#2018
mysql> select month('2018-01-17');#1
mysql> select day('2018-01-17');#17
mysql> select day('09:10:29');#9
mysql> select minute('09:10:29');#10
mysql> select second('09:10:29');#29

mysql> select week(now());#0
mysql> select year(now());#2018
mysql> select month(now());#1
mysql> select day(now());#4
mysql> select hour(now());#14
mysql> select minute(now());#59
mysql> select second(now());#20
# mysql终端运行实例
mysql> select week(now()),month(now()),day(now());
+-------------+--------------+------------+
| week(now()) | month(now()) | day(now()) |
+-------------+--------------+------------+
|           2 |            1 |         4  |
+-------------+--------------+------------+
```

unix_timestamp():字符串转时间戳
``` bash
mysql> select unix_timestamp("2018-01-04 14:37:44");#1515047864
```

from_unixtime():时间戳转字符串
``` bash
mysql> select from_unixtime(1515047864);#2018-01-04 14:37:44
```
date_format():字符串时间格式化
``` bash
mysql> select date_format("2018-01-04 14:37:44","%Y/%m/%d %H:%i:%s");#2018/01/04 14:37:44
```
