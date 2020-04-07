---
title: 对子系统的php7安装swoole扩展
date: 2019-01-12 09:12:00
tags: 
    - Swoole
    - WSL
---

> "我现在只想静一静思考下后面的路如何走"

swoole 仅支持 Linux、FreeBSD、MacOS 三种操作系统，幸运的是现在的 Windows10 平台内置了 WSL(Windows Subsystem for Linux)，可以让我们更方便的使用 Linux 的一些命令及特性。

<!-- more  -->

目前的子系统都已经是 16.04 了，我们可以通过 `lsb_release -a` 该命令来查看，我的子系统信息如下

```
airvip@airvip:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 16.04.6 LTS
Release:        16.04
Codename:       xenial
```

# 安装 PHP


如果自系统中没有安装 php7,我们可以直接进行安装

```
$ sudo apt -y install php7.0 php7.0-curl php7.0-gd php7.0-gmp php7.0-json php7.0-mysql php7.0-opcache php7.0-readline php7.0-sqlite3 php7.0-tidy php7.0-xml  php7.0-bcmath php7.0-bz2 php7.0-intl php7.0-mbstring  php7.0-mcrypt php7.0-soap php7.0-xsl  php7.0-zip
```

安装好 php 之后，官方推荐使用 `pecl install swoole` 来安装 swoole,我尝试了几次都无法正常安装，所以选择源码编译安装。

# 编译安装 swoole 扩展

下载源码

```
$ wget  https://pecl.php.net/get/swoole-4.2.13.tgz
```

解压

```
$ tar -zxvf swoole-4.2.13.tgz
```

进入目录

```
$ cd swoole-4.2.13
```

使用 phpize 安装扩展

```
$ phpize
$ ./configure --help
$ ./configure
# 执行完 ./configure 多了些文件，将 config.h 中 #define HAVE_SIGNALFD 1 行注释掉即可

# 编译
$ sudo make  

# 出现如下内容，说明编译完成
----------------------------------------------------------------------
Libraries have been installed in:
   /home/airvip/swoole-4.2.13/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the 'LD_RUN_PATH' environment variable
     during linking
   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.


# 编译安装
$ sudo make install
Installing shared extensions:     /usr/lib/php/20151012/
Installing header files:          /usr/include/php/20151012
```

# 为php添加扩展

在php模块中添加扩展

```
$ cd /etc/php/7.0/mods-available/
$ sudo vim swoole.ini
# vim 后输入 extension=swoole.so
```

创建软连接

```
$ cd /etc/php/7.0/cli/conf.d/
$ sudo ln -s ../../mods-available/swoole.ini 20-swoole.ini
$ cd /etc/php/7.0/fpm/conf.d/
$ sudo ln -s ../../mods-available/swoole.ini 20-swoole.ini
```

查看php扩展

```
$ php -m
```

![phpm](https://s1.ax1x.com/2020/04/07/Gcp3vT.png)
