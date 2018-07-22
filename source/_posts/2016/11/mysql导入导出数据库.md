---
title: mysql导入导出数据库
date: 2016-11-06 08:22:56
tags:
    - Mysql
---

> At least I tried,At least I did that

MySQL是一个关系型数据库管理系统，由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下公司。

<!-- more -->

# Windows 系统

## 导出

1.导出整个数据库(e:/表示E盘根目录)

mysqldump是在cmd里面运行，不是在mysql数据库里面

mysqldump -h服务器地址 -u用户名 -p 数据库名 > 导出的文件名

``` Bash
mysqldump -h127.0.0.1 -udb_user -p db_name > e:/db_name.sql
```

回车之后会输入root用户的密码

2.导出一个表

mysqldump -h服务器地址 -u用户名 -p 数据库名 表名> e:/导出的文件名

``` Bash
mysqldump -h127.0.0.1 -udb_user  -p db_name table_name> e:/table_name.sql
```

3.导出一个数据库结构

``` Bash
mysqldump -127.0.0.1 -udb_user   -p -d --add-drop-table db_name >e:/db_name.sql
```

-d 没有数据 --add-drop-table 在每个create语句之前增加一个drop table

## 导入

1.导入数据库

常用source 命令

进入mysql数据库控制台，如

``` Bash
mysql -h127.0.0.1 -uroot -p
mysql>use 数据库
# 然后使用source命令，后面参数为脚本文件(如这里用到的.sql)
mysql>source e:/db_name.sql
```

2. 导入数据到数据库（cmd）

mysql -uroot -D数据库名 

3. 导入数据到数据库中得某个表(cmd)

mysql -uroot -D数据库名  表名


# Linux 系统

## 导出

数据库用mysqldump命令（注意mysql的安装路径，即此命令的路径）：

1、导出数据和表结构：

mysqldump -h127.0.0.1 -u用户名 -p 数据库名 > 数据库名.sql

``` Bash
# /usr/local/mysql/bin/   mysqldump -h127.0.0.1 -uroot -p abc > abc.sql
```
敲回车后会提示输入密码

2、只导出表结构

mysqldump -h127.0.0.1 -u用户名 -p密码 -d 数据库名 > 数据库名.sql

``` Bash
# /usr/local/mysql/bin/   mysqldump -127.0.0.1 -uroot -p -d abc > abc.sql
```

注：`/usr/local/mysql/bin/`  表示的是mysql的目录

## 导入

1、首先建空数据库

``` Bash
# mysql>create database abc;
```

2、导入数据库

方法一：在mysql里面操作

``` Bash
mysql>use abc; # 选择数据库
mysql>set names gbk; # 设置数据库编码
mysql>source /home/abc/abc.sql; # 导入数据（注意sql文件的路径）
```

方法二(bash超级终端)：

mysql -h127.0.01 -u用户名 -p密码 数据库名 < 数据库名.sql

``` Bash
#mysql -h127.0.0.1 -uroot -p abc < abc.sql
```