---
title: Mysql的定时任务执行相关操作
date: 2016-06-01 23:24:23
tags:
    - Mysql
---

> 最最好的，与最最痛苦的，是一样的。

mysql的定时任务,在默认情况下是没有开启的，我们可以通过配置，开启mysql的定时任务，这样放便我们以后使用mysql来执行一些定时任务。

<!-- more -->

自 MySQL5.1.6起，增加了一个非常有特色的功能–事件调度器(Event Scheduler)，可以用做定时执行某些特定任务（例如：删除记录、对数据进行汇总等等），来取代原先只能由操作系统的计划任务来执行的工作。更值得 一提的是MySQL的事件调度器可以精确到每秒钟执行一个任务，而操作系统的计划任务（如：Linux下的CRON或Windows下的任务计划）只能精 确到每分钟执行一次。对于一些对数据实时性要求比较高的应用（例如：股票、赔率、比分等）就非常适合。

# 事件调度器设置

查看是否开启事件调度器（没开）

``` Sql
mysql> show variables like 'event_sch%';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| event_scheduler | OFF   |
+-----------------+-------+
1 row in set (0.00 sec)
```

mysql默认情况下是没有开启该功能的，可以在mysql.ini(windows)或者my.cnf(linux/unix)中开启，添加event_scheduler = ON 如下：
``` Sql
[root@localhost ~]#vi  /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
[mysqld]
event_scheduler = ON 
#
```

重启mysql服务器
``` Sql
[root@localhost ~]# systemctl restart mysqld
```

查看是否开启事件调度器（已开）
``` Sql
mysql> show variables like 'event_sch%';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| event_scheduler | ON    |
+-----------------+-------+
1 row in set (0.01 sec)
```

# 定时任务

创建定时任务
``` Sql
mysql> CREATE EVENT truntb
    -> ON SCHEDULE 
    ->     EVERY 1 MINUTE
    -> DO 
    ->     truncate wzbdb.user;
Query OK, 0 rows affected (0.00 se
```

若是在凌晨1点执行操作，修改执行时间如下
``` Sql
EVERY 1 DAY STARTS '2016-06-01 01:00:00'
```

在mysql.event中查看定时任务
``` Sql
mysql> select * from event\G
```
![mysql crond](https://s2.ax1x.com/2020/02/06/1ytr4A.jpg)


# 测试
``` Sql
//插入测试数据
mysql> insert into `user` (`name`) values ('wzb');
Query OK, 1 row affected (0.07 sec)
//查看测试数据
mysql> select * from user;
+----+------+
| id | name |
+----+------+
|  1 | wzb  |
+----+------+
1 row in set (0.01 sec)
//过了一会，再次查看
mysql> select * from user;
Empty set (0.01 sec)
```

# 删除定时任务
``` Sql
mysql> DELETE FROM mysql.event WHEwzbdb' AND name='truntb' 
AND definer='root@localhost';
Query OK, 1 row affected (0.01 sec)
```