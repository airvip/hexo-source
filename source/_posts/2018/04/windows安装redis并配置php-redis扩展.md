---
title: windows安装redis并配置php-redis扩展
date: 2018-04-03 11:12:23
tags: 
    - Windows
    - Redis
---

> 懂得认输是非常重要的，不尝到失败的不甘，也就不会明白跌倒后怎么爬起来，更不可能再向前迈进。

REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。

Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

<!-- more -->

它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Map), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

作为开发人员，在我们开发中使用 redis 是提高性能非常重要的一节。

## 下载安装

先通过以下方式直接下载安装包
[中文官网下载](http://www.redis.net.cn/)
[github下载](https://github.com/MicrosoftArchive/redis/releases)

下一步，下一步安装就可以了，记得要加入path目录,安装完之后进行重启即可。
![redis_install](/img/201804/redis-php/redis_install.png)
![redis_finish](/img/201804/redis-php/redis_finish.png)

**开启关闭redis服务**
开启服务：redis-server --service-start
停止服务：redis-server --service-stop
![redis_use](/img/201804/redis-php/redis_use.png)

## 安装php-redis扩展

1、使用 phpinfo() 函数查看PHP的版本信息，这会决定扩展文件版本。
![phpinfo](/img/201804/redis-php/phpinfo.png)
**小心坑**：可以看到我们的php信息是 vc14 x86 NTS 线程不安全的

2、下载php_igbinary，php_redis.zip（根据自己的php版本信息下载，一定要保证版本的正确性）

[下载php_igbinary-2.0.5-7.0-nts-vc14-x86.zip](http://windows.php.net/downloads/pecl/releases/igbinary/)

[下载php_redis-3.1.6-7.0-nts-vc14-x86.zip](https://windows.php.net/downloads/pecl/releases/redis/)

3、分别解压缩，ps:ext 目录在你安装使用的 php 目录中的一个文件夹
将 php_igbinary.dll 和 php_igbinary.pdb 拷贝至 php 的 ext 目录下
将 php_redis.dll 和 php_redis.pdb 拷贝至 php 的 ext 目录下

4、修改 php.ini(ps:php.ini 文件在你安装使用的 php 目录下) 在该文件中加入以下代码
```
; php_redis
extension=php_igbinary.dll
extension=php_redis.dll
```
*extension=php_igbinary.dll 一定要放在 extension=php_redis.dll 的前面，否则此扩展不会生效*

5、重启服务器通过 phpinfo() 函数查看我们新加的扩展，或者使用 php -m查看我们的扩展
![phpm](/img/201804/redis-php/phpm.png)

到此 phpredis 扩展安装完成，其中有几处坑，提醒大家小心！



