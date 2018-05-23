---
title: MySQL server has gone away的解决方法
date: 2018-01-02 23:22:37
tags:
    - Mysql
---

> 彼此相互推卸不幸是没有结果的。

因项目需要，需要把生产环境中的一张表导入到本地数据库进行操作，sql 文件大约 100M 的样子，在实践中竟然遇到了 MySQL server has gone away 的问题，现在在此记录一下解决办法。

<!-- more -->

# 错误产生

把表的数据及结构下载到本地之后，开始直接进行导入操作，如下

``` bash
#使用 test 数据库
mysql> use test
Database changed
#进行导入
mysql> source C:\Users\Admin\Desktop\questions_log.sql
Query OK, 0 rows affected (0.07 sec)

ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    6
Current database: test

ERROR 2006 (HY000): MySQL server has gone away
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    7
Current database: test

Query OK, 0 rows affected (1.01 sec)
```

从上面可以看到非常明显的报错信息`MySQL server has gone away`

# 解决方法

通过查看官方手册，发现这么一个参数 `max_allowed_packet` ,适当增大 `max_allowed_packet` 参数可以使 client 端到 server 端传递大数据时，系统能够分配更多的扩展内存来处理。

先查看下现在的 mysql 中 `max_allowed_packet` 值是多少

``` bash
mysql> show global variables like 'max_allowed_packet';
+--------------------+---------+
| Variable_name      | Value   |
+--------------------+---------+
| max_allowed_packet | 1048576 |
+--------------------+---------+
1 row in set (0.00 sec)
```

毫无疑问，`1024*1024*1=1048576`,也就是说是 1M,肯定是不够的，现在调整为 100 M，`1024*1024*100 = 104857600`

操作如下

``` bash
mysql> set global max_allowed_packet=104857600;
Query OK, 0 rows affected (0.00 sec)

mysql> show global variables like 'max_allowed_packet';
+--------------------+-----------+
| Variable_name      | Value     |
+--------------------+-----------+
| max_allowed_packet | 104857600 |
+--------------------+-----------+
1 row in set (0.00 sec)
```

再次导入，执行正常。

# 友情提醒
使用 `set global max_allowed_packet=104857600` 命令修改 max_allowed_packet 的值，重启 mysql 后会失效，还原为默认值。

如果想永久修改，mysql 重启后不还原，
Linux 可以打开 my.cnf 文件，添加 `max_allowed_packet = 100M` ;
Windows 可以打开 my.ini 文件，添加 `max_allowed_packet = 100M` ;