---
title: Ubuntu16.04下对php7安装event扩展
date: 2019-05-08 10:24:55
tags: 
    - Php
    - Workman
---

> 为遇一人入红尘，人去我亦去，此生不留尘。

正常情况下玩 workerman 即便不安装 event 扩展也是可以的，但是如果业务并发连接数大于 1024，强烈推荐安装 event 扩展或者  libevent 扩展，并且对 Linux 内核进行优化。

<!-- more  -->

event 扩展或者 libevent 扩展作用相同，二选一即可。
but，由于目前为止，libevent 扩展支持 php5.3-5.6，目前还不支持php7。so，作为 php7 用户的我们只能使用 event 扩展。

* 注意：libevent 扩展也同样依赖 libevent 库，所以首先需要安装 libevent-dev 包(并非扩展)。


## 安装开始

```
# 如果没有安装 phpize，请先安装
$ sudo apt install php7.0-dev
# 安装 libevent-dev 包
$ sudo apt-get -y install libevent-dev
# 安装 event 扩展
$ sudo pecl install event
```
安装event
![install_event](/img/201905/event/install_event.jpg)

提示：Include libevent OpenSSL support [yes] : 时输入no回车，
提示：PHP Namespace for all Event classes :时输入yes，其它直接敲回车就行

安装成功
![install_completed](/img/201905/event/event_completed.jpg)

```
# 查看 php 实际加载的 ini 文件路径
$ php --ini
Configuration File (php.ini) Path: /etc/php/7.0/cli
Loaded Configuration File:         /etc/php/7.0/cli/php.ini
Scan for additional .ini files in: /etc/php/7.0/cli/conf.d
Additional .ini files parsed:      /etc/php/7.0/cli/conf.d/10-mysqlnd.ini,
/etc/php/7.0/cli/conf.d/10-opcache.ini,
/etc/php/7.0/cli/conf.d/10-pdo.ini,
# 添加 php 扩展配置
$ sudo vim /etc/php/7.0/cli/php.ini
# 可以在最后追加 event 扩展配置， 
extension=event.so
```
也可以直接使用 `echo extension=event.so >> /etc/php/7.0/cli/php.ini` 来进行直接追加配置

查看 event 扩展是否安装成功
```
$ php -m
[PHP Modules]
calendar
Core
ctype
curl
date
dom
event
```

## 常见问题

1. 如果报错提示没有 pear ，需要你安装 `sudo apt-get -y install php-pear`

2. 如果报错：checking for include/event2/event.h... not found
configure: error: Please reinstall the event library, or provide the installation prefix via --with-event-libevent-dir option
是需要你安装 libevent-dev 包 `sudo apt-get -y install libevent-dev`

3. 如果报错：NOTICE: PHP message: PHP Warning: PHP Startup: Unable to load dynamic library '.../event.so' - ..../event.so: undefined symbol: php_sockets_le_socket in Unknown on line 0。 
是需要你更改 event.so 和 socket.so 的加载顺序，即：在 php.ini 中将 extension=socket.so 写在 extension=event.so 前面，让 socket 扩展先加载。


