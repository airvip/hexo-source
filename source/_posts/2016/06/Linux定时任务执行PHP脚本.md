---
title: Linux定时任务执行PHP脚本
date: 2016-06-01 23:08:09
tags:
    - Linux
    - Php
---

> 有开枪资格的，只有那些有觉悟被射杀的人。

平时我们经常会用到定时任务去执行一些操作，关于使用linux的定时任务，执行php脚本，现在写了一个demo,为方便以后查看。

<!-- more -->

# 操作平台

查看当前操作系统
``` bash
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-229.el7.x86_64 #1 
SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 7.1.1503 (Core)
```

查看php安装位置
``` bash
[root@localhost ~]# which php
/usr/bin/php
```
或者使用
``` bash
[root@localhost ~]# whereis php
php: /usr/bin/php /usr/lib64/php /etc/php.d /etc/php.ini 
/usr/include/php /usr/share/php /usr/share/man/man1/php.1.gz
```

# php文件

php脚本所在位置`/root/clear.php`

php脚本内容
``` bash
[root@localhost ~]#vi clear.php
#!/usr/bin/php -q
<?php
$host = 'localhost';
$user = 'wzb';
$pass = 'Iloveyou.123456';
$db   = 'wzbdb';
$port  = '3306';
//connect mysql
$mysqli = new mysqli($host,$user,$pass,$db,$port);
//判断是否有错
if($mysqli->connect_errno){
    echo 'connect error'.$mysqli->connect_error;
    exit();
}
//清空wzbdb下的user表
$mysqli->query("truncate user");
//关闭连接
$mysqli->close();
```

编写定时任务（每分钟执行一下php脚本）
``` bash
[root@localhost ~]# crontab -e
* * * * * /usr/bin/php /root/clear.php
```

查看当前有哪些任务调度
``` bash
[root@localhost ~]# crontab -l
* * * * * /usr/bin/php /root/clear.php
```

终止任务调度
``` bash
[root@localhost ~]# crontab -r
```