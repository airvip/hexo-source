---
title: Linux(centos7)下yum安装(lamp)php-mysql
date: 2016-01-29 16:04:34
tags:
    - Linux
    - Centos
---

> 下雨了，可是我等的你在哪里？

php-mysql是一个比较老的驱动，php5.3之后推荐使用php-mysqlnd，在本篇文章中我们依旧使用php-mysql驱动连接数据库，在博客最后我们会更新为php-mysqlnd驱动。

<!-- more -->

Lamp中的linux已经安装完毕,现在就该配置配置了。

当前操作系统环境

``` bash
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]# cat /etc/redhat-release
CentOS Linux release 7.1.1503 (Core)
```

# 安装php-mysql

查看是否安装php-mysql

``` bash
[root@localhost ~]# yum list installed | grep php
```

查看yum库有没有php-mysql

``` bash
[root@localhost ~]# yum list | grep php-mysql 
```

开始安装

``` bash
[root@localhost ~]# yum -y install php-mysql 
```

![php-mysql](/img/201601/php-mysql/php-mysql.jpg)

重启httpd服务

``` bash
[root@localhost ~]# systemctl restart httpd
```

# 环境测试

测试PHP能否连接到MySQL数据库
在/var/www/html目录下新建一个文档test.php。
之前已经在MySQL中新建了数据库wzbdb，建了个用户wzb，密码为Iloveyou.123456

test.php 文件代码如下

``` bash
[root@localhost ~]# cd /var/www/html
[root@localhost html]# vi test.php
<?php
$HOST = 'localhost';
$USER = 'wzb';
$PASS = 'Iloveyou.123456';
$DB = 'wzbdb';
$PORT = '3306';

$mysqli = new mysqli($HOST, $USER, $PASS, $DB,$PORT); 

if ($mysqli->connect_errno) {
    printf("Connect failed: %s\n", $mysqli->connect_error);
    exit();
}
echo 'db test connected ok';
?>
```

浏览器测试
![php-mysql-res](/img/201601/php-mysql/php-mysql-res.jpg)

用php-mysqlnd替换php-mysql

``` bash
[root@localhost ~]#yum remove php-mysql
[root@localhost ~]#yum -y install php-mysqlnd
```