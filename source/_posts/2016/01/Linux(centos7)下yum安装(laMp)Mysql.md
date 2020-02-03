---
title: Linux(centos7)下yum安装(laMp)Mysql
date: 2016-01-29 11:03:22
tags:
    - Linux
    - Centos
    - Mysql
---

> 区区一只趴在地上的蝼蚁，谁允许你抬起头的， 虫子就要有虫子的样子，低头伏地然后去死。

LAMP是Linux+Apache+MySQL+PHP的简称，是用于搭建web服务器的一种解决方案。虽然从RHEL 7开始Red Hat公司推荐使用MariaDB而不是MySQL，但在我这篇文章当中，我还是决定继续使用MySQL。

<!-- more -->

为了要安装MySQL，我选择的是去官网下载安装包，不过在下载之前需要先注册。因为使用的是CentOS 7系统，所以我下载的是mysql57-community-release-el7-8.noarch.rpm这个文件。下载下来后，将它上传到/root目录下。

# 添加软甲源

将MySQL Yum Repository添加到系统的软件库列表（repositorylist）。

``` bash
[root@localhost ~]# ls
anaconda-ks.cfg  mysql57-community-release-el7-8.noarch.rpm
[root@localhost ~]# yum localinstall mysql57-community-release-el7-8.noarch.rpm 
.........直到出现complete!.............
```

检查是否添加成功

``` bash
[root@localhost ~]# yum repolist enabled | grep mysql
mysql-connectors-community/x86_64      MySQL Connectors Community            21
mysql-tools-community/x86_64           MySQL Tools Community                 33
mysql57-community/x86_64               MySQL 5.7 Community Server            74
```

# 安装MySQL

查看要下载的软件包是否存在

``` bash
[root@localhost ~]# yum list | grep mysql
.........省略...............
mysql-community-server.x86_64    5.7.12-1.el7    mysql57-community
...........省略............
```

安装mysql

``` bash
[root@localhost ~]# yum -y install mysql-community-server
```

![installmysql](https://s2.ax1x.com/2020/02/03/1UAnAK.jpg)

如图所示，这个命令会安装3个包30个包依赖，它会安装包括MySQL client、共享客户端库（mysql-community-libs）等软件。注意到，这里安装的MySQL版本为5.7.12,截至2016/1/29日最新版，肯定有好多坑要踩。

启动mysqld服务并将其设为开机启动：

``` bash
[root@localhost ~]# systemctl start mysqld
[root@localhost ~]# systemctl enable mysqld
```

检查mysqld服务状态

``` bash
[root@localhost ~]# systemctl status mysqld
```

![mysqld](https://s2.ax1x.com/2020/02/03/1UAe76.jpg)

查看mysqld服务侦听端口

``` bash
[root@localhost ~]# netstat -atulpn | grep mysqld
tcp6    0      0 :::3306     :::*        LISTEN      2722/mysqld 
```

MySQL侦听tcp端口3306。<span style="color:red">防火墙并未放通该端口，其它设备上是无法访问本服务器的MySQL数据库的。</span>这里的MySQL也仅给本机的PHP使用的，就不必放通tcp端口3306。

根据MySQL官方手册，建议安装完MySQL数据库后，使用下面的命令来

增强数据库的安全性

``` bash
[root@localhost log]# mysql_secure_installation
```

* 该命令程序可以协助你设置数据库root账号的密码，(5.7.12密码要很复杂，如)`Iloveyou.123456`
* 移除匿名用户账号，y
* 禁用远程登录root账号，n
* 移除自带的test数据库，n(结果还是移除了)
* 是否要重新加载特权表以让修改生效。y

传送门：[详细官方文档（注：全是英文）](http://dev.mysql.com/doc/refman/5.7/en/mysql-secure-installation.html)

你可以设置数据库root账号密码后，其它的都选是(Y)。

*如果不想使用上面的命令*，那可以使用下面的命令来给root账号设置密码，回车后它会提示你输入密码的

``` bash
[root@localhost log]# mysql_secure_installation
```

关于这个命令我不想在这里说太多，详情我会分析单独作为一篇博客来写。

<span style="color:red">上面的命令回车后需要输入原来的旧密码，</span>

查找旧密码

``` bash
[root@localhost ~]# cd /var/log
[root@localhost log]# cat mysqld.log
有这么一句
2016-05-02T08:15:08.594076Z 1 [Note] A temporary password is generated for root@localhost: BCFE=og?h9%X
对是它，就是它
```

提示修改root账号的密码：mysqladmin -u root password不灵了

Mysql几条命令解释

``` bash
连接数据库
[root@localhost ~]# mysql -h127.0.0.1 -uroot -p
Enter password: 
新建本地用户wzb,密码Iloveyou.123456，在修改root密码时就特坑，密码太简单，建用户创建密码还是这么复杂，让我在旧版本常用123的情何以堪
mysql> create user 'wzb'@'localhost' identified by 'Iloveyou.wzb';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> create user 'wzb'@'localhost' identified by 'Iloveyou.123456';
Query OK, 0 rows affected (0.00 sec)
创建数据库wzbdb,本来是没移除test数据库的，结果莫名奇妙的移除了，不知是不是bug
mysql> create database wzbdb;
Query OK, 1 row affected (0.01 sec)
将wzbdb数据库的所有权分配给本地用户wzb
mysql> grant all privileges on wzbdb.* to wzb@localhost;
Query OK, 0 rows affected (0.01 sec)
刷新系统权限表
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
进入数据库mysql,mysql是系统自带库
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
去查看新增加的本地用户wzb
mysql> select * from user where user='wzb'\G
*************************** 1. row ***************************
                  Host: localhost
                  User: wzb
           Select_priv: N
           Insert_priv: N
           Update_priv: N
           Delete_priv: N
           Create_priv: N
             Drop_priv: N
           Reload_priv: N
         Shutdown_priv: N
          Process_priv: N
             File_priv: N
            Grant_priv: N
       References_priv: N
            Index_priv: N
            Alter_priv: N
          Show_db_priv: N
            Super_priv: N
 Create_tmp_table_priv: N
      Lock_tables_priv: N
          Execute_priv: N
       Repl_slave_priv: N
      Repl_client_priv: N
      Create_view_priv: N
        Show_view_priv: N
   Create_routine_priv: N
    Alter_routine_priv: N
      Create_user_priv: N
            Event_priv: N
          Trigger_priv: N
Create_tablespace_priv: N
              ssl_type: 
            ssl_cipher: 
           x509_issuer: 
          x509_subject: 
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: *DD7C95F74053D2600490481F44305B5272452D48
      password_expired: N
 password_last_changed: 2016-05-02 04:26:16
     password_lifetime: NULL
        account_locked: N
1 row in set (0.00 sec)
查看数据库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wzbdb              |
+--------------------+
5 rows in set (0.00 sec)
退出
mysql> exit;
Bye
```

设定好后，就可以使用wzb登录MySQL了，查看数据库。

``` bash
[root@localhost ~]# mysql -hlocalhost -uwzb -p
Enter password: 

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| wzbdb              |
+--------------------+
2 rows in set (0.00 sec)
```

关于mysql

* /etc/my.cnf：这是MySQL的配置文件。可能的操作就是设置字符集
* /var/lib/mysql：这是数据库实际存放目录。毫无疑问，不能删，并且，要注意，给予其所在分区足够的容量。
* /var/log/mysqld.log：这是MySQL的错误日志文件。