---
title: Mysql数据库常用操作
date: 2016-02-15 13:12:22
tags:
    - Mysql
---

> 当世界背叛你的那天到来时，我会为你背叛全世界。

mysql数据库是一款流行的关系型数据库，在此记录一下常用命令，方便以后查询。

<!-- more -->

# 导入导出

导出整个数据库
```
D:\download>mysql\bin\mysqldump -uroot -p `db_name`>e:/wzb.sql
```

导出数据库某张表
```
D:\download>mysql\bin\mysqldump -uroot -p `db_name` `tab1_name` `tab2_name`>e:/wzbtab.sql
```

导入数据库
```
mysql> source C:\Users\wzb\Desktop\airblog.sql
```

# 库操作

创建数据库
```
mysql> create database `php_one`;
```

查看当前数据库
```
mysql> show databases;
```

查看数据库的创建语句
```
mysql> show create database `php_one`;
```

删除数据库
```
mysql> drop database `php_one`;
mysql> drop database if exists `php_one`;
```

查看服务器字符集
```
mysql>show variables like 'character_set%';
```

使用某个数据库
```
mysql>use `php_one`;
```

# 表操作

创建表
```
mysql>CREATE TABLE IF NOT EXISTS `php_one`.`wd_admin` (
  `id` INT NOT NULL AUTO_INCREMENT COMMENT '管理员表的主键',
  `account` VARCHAR(20) NOT NULL DEFAULT '' COMMENT '管理员帐号',
  `password` CHAR(32) NOT NULL DEFAULT '' COMMENT '管理员密码',
  `logintime` INT(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '上次登录的时间',
  `loginip` CHAR(20) NOT NULL DEFAULT '' COMMENT '上次登录的ip',
  `locks` TINYINT(1) UNSIGNED NOT NULL DEFAULT 0 COMMENT '0没锁定1锁定',
  PRIMARY KEY (`id`),
  INDEX `account` (`account` ASC))
ENGINE = INNODB
COMMENT = '后台管理员';
```

查看当前数据表
```
mysql>show tables;
```

# 表数据的增删改查

插入数据
```
mysql>insert into `tab_name` (`account`,`password`) values ('12345','12345'),('1234','1234'); 
```

查询数据
```
mysql>select `account`,`password` from `tab_name` where `fenshu`>=15 order by `fenshu` asc,`date_time` desc;
```

起别名查询
```
mysql>select t.id as t_id,t.t_name,tc.id as tc_id,tc.begin_date from teacher as t left outer join teacher_class as tc on teacher.id=teacher_class.t_id;
```

删除数据
```
mysql>delete from `tab_name` order by `create_time` desc limit 2;
```


清空表
```
mysql>truncate `tab_one`;
```

单表更新数据
```
mysql>update `tab_name` set `lie_name`='lie_zhi' where id=12;
```

多表更新数据
```
mysql>update one join two on one.public_field=two.public_field set one_data='x',two_data='y' where one_id=2;
```