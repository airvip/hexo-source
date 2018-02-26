---
title: win7上安装了mysql5.6没有安装服务
date: 2016-09-24 14:52:30
tags:
    - Mysql
    - Windows
---

> 彼此相互推卸不幸是没有结果的。

最近把电脑系统重做了一下，安装mysql时，遇到了一点小问题,现将问题记录下来，方便之后对相似问题的解决。

<!-- more -->

## 问题

在 win7 上安装了 mysql-5.6.10.msi 之后，发现并没有注册服务，致使无法启动 mysql，问题很简单，在这里记录一下解决方法。

## 解决

在cmd中，进入自己的mysql安装目录下的bin目录，里面有一个mysqld.exe，执行mysqld.exe install即可。

``` bash
D:\deve\mysql\bin>mysqld.exe install
```
![mysqld 安装](/img/201609/mysql_noserver/mysqld_install.png)
有可能会出现  Install/Remove of the Service Denied! 问题，是因为当前用户权限不足导致的。

解决权限不足问题

1. 先进入系统安装盘符（我的是在c盘）

2. 按如下路径找到cmd 
```bash
C:\Windows\System32\cmd.exe
```
3. 右键  以管理员身份运行(A)

4. 在 cmd 中进入 mysql 安装目录的 bin 目录，执行 `mysql.exe install` 即可

## 设置root密码

由于 mysql5.6 的 root 密码默认为空，所以先给 root 设置密码 

按路径  (桌面》计算机右键》管理》服务》mysql) 启动服务

在cmd中按下面的方式进入，之让输入密码时，直接回车就进入mysql了
``` bash
D:\deve\mysql\bin>mysql -h127.0.0.1 -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.10 MySQL Community Server (GPL)
...
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
# 开始设置密码
mysql> set password for root@localhost = password('123456');
Query OK, 0 rows affected (0.00 sec)
```

现在就可以退出使用新设置的密码登录了。