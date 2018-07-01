---
title: Apache启动、重启、关闭win与linux命令
date: 2016-10-19 13:29:16
tags:
    - Apache
---

> 世界上没有一个人能代替另一个人。

Apache是世界使用排名第一的Web服务器软件。它可以运行在几乎所有广泛使用的计算机平台上，由于其跨平台和安全性被广泛使用，是最流行的Web服务器端软件之一。

<!-- more -->

Apache奔跑在Linux上，然而由于现在的开发者，很多都是用windows做开发，所以本文将给出linux与windows相关命令，而其他 Unix like 机器上的操作参考linux的命令.

* 知道安装目录很重要，即便大家默认安装软件也要仔细看好软件的安装在哪个目录

# Linux(centos)

1、启动

``` Bash
$ /alidata/server/httpd/bin/apachectl start apache
```

2、重新启动

``` Bash
$ /alidata/server/httpd/bin/pachectl restart 
```

要在重启 Apache 服务器时不中断当前的连接，则应运行

``` Bash
$ /alidata/server/httpd/bin/apachectl graceful
```

3、停止

``` Bash
$ /alidata/server/httpd/bin/apachectl stop 
```

如果apache安装成为linux的服务的话，可以用以下命令操作

``` Bash
$ service httpd start
$ service httpd restart
$ service httpd stop
```

# windows(Win7)

为方便操作，请将apache加入环境变量

1、启动或者

``` Bash
> net start apache2.4(我的是2.4)
```

或者(加环境变量后)

``` Bash
> httpd -k start
```

2、重启

``` Bash
> httpd -k restart
```

3、停止

``` Bash
> net stop apache2.4 # 方式一
> httpd -k shutdown # 方式二
> httpd -k stop # 方式三
```

**最最重要的命令是多用 --help**