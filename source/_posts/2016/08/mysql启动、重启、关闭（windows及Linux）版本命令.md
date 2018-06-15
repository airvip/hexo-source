---
title: mysql启动、重启、关闭（windows及Linux）版本命令
date: 2016-08-13 11:54:33
tags:
    - Mysql
---

> 我一直都在你身边 ，一直都在。

MySQL是一个关系型数据库管理系统，由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下公司。

<!-- more -->

mysql是中小型网站最受欢迎的一款数据库，其跨平台型好，在windows和linuxs下都有着很大的市场占有率。所以本文将以 Linux 和windows为例讲解, 而其他 Unix like 机器参考Linux的命令。

* 知道安装目录很重要，即便大家默认安装软件也要仔细看好软件的安装在哪个目录

# 管理Linux中的mysql

Linux(centos)版本（以阿里云web一键安装包为例）
阿里云web一键安装包已经把mysql安装成了linux服务

1、启动

``` bash
[root@localhost ~]# service mysqld start
```

2、重启

``` bash
[root@localhost ~]# service mysqld restart
```

3、停止

``` bash
[root@localhost ~]# service mysqld stop
```

4、查看是否启动

``` bash
[root@localhost ~]# ps -A | grep mysqld
 2767 pts/0    00:00:00 mysqld_safe
 3068 pts/0    00:00:03 mysqld
```

# 管理Windows中的mysql

如果想方便以后操作，最好把mysql安装目录的mysql加入到环境变量,方便连接数据库与数据库操作。

1、启动

``` bash
D:\>net start mysql
```

2、停止

``` bash
D:\>net stop mysql
```

windows下没有直接重启，但是可以通过批处理完成 。
操作如下：先新建 mysqlreset.txt,写入下面代码，在重命名为 mysqlreset.bat

``` txt
net stop mysql
net start mysql
```



