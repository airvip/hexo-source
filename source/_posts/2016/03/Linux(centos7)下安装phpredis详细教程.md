---
title: Linux(centos7)下安装phpredis详细教程
date: 2016-03-01 01:20:00
tags:
    - Redis
    - Linux
---

> 以前高中时，羡慕大学考试只要及格就行；现在大学了，怀念高中考试及不及格都行…

phpredis是php的一个扩展，效率是相当高有链表排序功能，对创建内存级的模块业务关系很有用;现将phpredis的安装过程记录下来，方便大家共享。

<!-- more -->

## 下载安装
1、下载phpredis扩展

可以前往github下载 传送门：[点击进入github下载](https://github.com/owlient/phpredis)

可以使用wget下载
``` bash
[root@localhost ~]# wget https://codeload.github.com/nicolasff/phpredis/zip/master
```

2、解压phpredis到当前目录
``` bash
[root@localhost ~]# unzip phpredis-master.zip -d ./
```

3、进入解压的目录
``` bash
[root@localhost ~]# cd phpredis-master
```

4、查看phpize文件的位置，通过phpize建立php的外挂模块
``` bash
4.1 查看phpize在哪
[root@localhost phpredis-master]# whereis phpize
phpize: /usr/bin/phpize /usr/share/man/man1/phpize.1.gz
4.2 运行phpize建立扩展模块
[root@localhost phpredis-master]# /usr/bin/phpize 
Configuring for:
PHP Api Version:         20100412
Zend Module Api No:      20100525
Zend Extension Api No:   220100525
如果在4.2遇到一下错误
Can't find PHP headers in /usr/include/php
The php-devel package is required for use of this command.
请先安装php-devel,再执行4.2
yum -y install php-devel
```

5、查看php-config文件的位置，运行redis的配置文件
``` bash
5.1 查看php-config在哪
[root@localhost phpredis-master]# whereis php-config
php-config: /usr/bin/php-config /usr/share/man/man1/php-config.1.gz
5.2 运行phpredis配置
[root@localhost phpredis-master]# ./configure --with-php-config=/usr/bin/php-config 
如果报了下面的错误
checking for gcc... no
请安装gcc，再执行5.2
[root@localhost phpredis-master]# yum -y install gcc
```

6、编译安装
``` bash
[root@localhost phpredis-master]# make && make install
----------------------------------------------------------------------
Libraries have been installed in:
   /root/phpredis-master/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/lib64/php/modules/
```

## 配置

7、编辑php.ini文件，添加如下代码
``` bash
[root@localhost phpredis-master]# vi /etc/php.ini
; extension_dir = "./"
;编译phpredis扩展时启用时间为2016/5/10
extension_dir = "/usr/lib64/php/modules/"
extension=redis.so
```
在下面的测试中出问题后再添加也可以。

**提醒：第7步可以根据测试选择跳过，第10步是注释掉第7步之后的测试结果**
``` bash
当添加第7步时，在命令行下测试报了redis重复载入，我就注释掉了第7步的代码
[root@localhost html]# php redis_test.php 
PHP Warning:  Module 'redis' already loaded in Unknown on line 0
www.dear521.com
我的php版本
[root@localhost html]# vi /etc/php.ini
[root@localhost html]# php -v
PHP 5.4.16 (cli) (built: Jun 23 2015 21:17:27) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
```

## 测试

8、测试
``` bash
[root@localhost html]# vi phpinfo.php 
<?php
    phpinfo();
?>
```
![phpredis](/img/201603/phpredis/phpredis.jpg)

9、通过php连接redis
``` bash
[root@localhost html]# cat redis_test.php 
<?php
    $redis = new Redis();
    $redis->connect('127.0.0.1',6379);
    $redis -> set('domain','www.dear521.com');
    echo $redis->get('domain');
?>
```

10、命令行下测试结果
``` bash
[root@localhost html]# php redis_test.php 
www.dear521.com
```
