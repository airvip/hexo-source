---
title: Linux(centos7)下yum安装(lamP)PHP
date: 2016-01-28 22:36:55
tags:
    - Linux
    - Centos
    - Php
---

> 缘起，在人群中，我看见你。缘灭，我看见你，在人群中。

PHP（外文名:PHP: Hypertext Preprocessor，中文名：“超文本预处理器”）是一种通用开源脚本语言。语法吸收了C语言、Java和Perl的特点，利于学习，使用广泛，主要适用于Web开发领域。

<!-- more -->

# 操作环境

当前操作系统环境

``` bash
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 7.1.1503 (Core) 
```

# 安装开始

安装php

``` bash
查看是否安装php
[root@localhost ~]# yum list installed | grep php
查看yum库有没有php
[root@localhost ~]# yum list | grep php 
php.x86_64                              5.4.16-36.el7_1                @base 
..............省略一堆代码.................
开始安装
[root@localhost ~]# yum -y install php
```

![php_install](/img/201601/php/php_install.jpg)

如图所示，安装的PHP版本为5.4.16。

说明    <span style="color:red">安装完成后</span>

* PHP会生成配置文件/etc/httpd/conf.d/php.conf，因为该配置文件在/etc/httpd/conf.d目录下，所以它会被Apache所读取。
* PHP还会生成配置文件/etc/httpd/conf.modules.d/10-php.conf，该配置文件也会被   Apache所读取，它的设定让Apache可以加载PHP模块。
* PHP软件本身的配置文件其实是/etc/php.ini。

# 相关配置文件

介绍配置文件/etc/httpd/conf.d/php.conf

``` bash
[root@localhost conf]# cd /etc/httpd/conf.d
[root@localhost conf.d]# vi php.conf
# Cause the PHP interpreter to handle files with a .php extension.
#<FilesMatch 正则表达式>和</FilesMatch>：文件名与正则表达式相匹配的文件，将适用这里设定的语句。   
#SetHandler：强制所有匹配的文件使用指定handler进行处理。               
<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>

# Allow php to handle Multiviews
#设定扩展名为.php的档案的类型为text/html     
AddType text/html .php
#
# Add index.php to the list of files that will be served as directory
# indexes.
#默认网页档名增加index.php   
DirectoryIndex index.php

#
# Uncomment the following lines to allow PHP to pretty-print .phps
# files as PHP source code:
#
#<FilesMatch \.phps$>
#    SetHandler application/x-httpd-php-source
#</FilesMatch>

#
# Apache specific PHP configuration options
# those can be override in each configured vhost
#
php_value session.save_handler "files"
php_value session.save_path    "/var/lib/php/session"
```

介绍配置文件/etc/httpd/conf.modules.d/10-php.conf：

``` bash
[root@localhost conf.modules.d]# cd /etc/httpd/conf.modules.d/
[root@localhost conf.modules.d]# vi 10-php.conf
# PHP is an HTML-embedded scripting language which attempts to make it
# easy for developers to write dynamically generated webpages.
#如果Apache使用的多处理模块(MPM)是prefork（默认），那么Apache就会加载LoadModule指定的模块(PHP模块) 
<IfModule prefork.c>
  LoadModule php5_module modules/libphp5.so
</IfModule>
```

介绍配置文件/etc/php.ini。

在该文件的开头处，有说明针对生产环境和开发环境，有一些设定值可以进行修改，以提高PHP的效能。

``` bash
[root@localhost etc]# cd /etc
[root@localhost etc]# ls | grep php
php.d
php.ini
[root@localhost etc]# vi php.ini
;假设该web服务器是用于生产环境的，将下面的这些设定值改成这样（或确认这些设定值是这样的）
;PHP可用的最大内存。可按照需要进行调整 
memory_limit = 128M
;设定PHP会告知的错误或信息类型
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
;当发生错误时，不将错误信息显示出来(到浏览器上)
display_errors = Off
;不将PHP启动时发生的错误信息显示出来(到浏览器上)
display_startup_errors = Off
;这让PHP可以将错误信息日志记录到日志文件里
log_errors = On
;忽略重复的错误信息 ，默认Off
ignore_repeated_errors = On
;当忽略重复的错误信息时忽略信息源 ，默认Off
ignore_repeated_source = On
;当PHP显示或记录错误信息时，将该信息格式化成HTML格式
html_errors = On
;PHP所允许的POST数据的最大值。可按照需要进行调整
post_max_size = 8M
;允许文件上传，默认为On
file_uploads = On
;上传文件所允许的最大值。可按照需要进行调整 ，默认2M
upload_max_filesize = 20M
```

# 相关测试

重启httpd服务

``` bash
[root@localhost~]# systemctl restart httpd
```

为了测试Apache能不能正常调用PHP，在/var/www/html目录下新建一个phpinfo.php文档，

code如下所示

``` bash
[root@localhost etc]# cd /var/www/html
[root@localhost html]# vi phpinfo.php
<?php
    phpinfo();
?>
```

保存之后在物理机（自己的主机）测试，看到如下界面

![test](/img/201601/php/test.jpg)


测试完后，这个页面里面的信息还挺重要的，<span style="color:red">推荐把phpinfo.php这个文件删除</span>。