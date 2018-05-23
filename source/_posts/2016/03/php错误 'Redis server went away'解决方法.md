---
title: php错误 'Redis server went away'解决方法
date: 2016-03-12 10:22:33
tags:
    - Redis
    - Php
    - Selinux
---

> 新闻联播的牛X之处在于就算你一直在换台，也能完整的看完一条新闻。

PHP Fatal error: Uncaught exception 'RedisException' with message 'Redis server went away' 错误的解决方法，在此记录，方便之后查询

<!-- more -->

# 错误调试

做测试时，在命令行可以正常运行，在Apache下以网页形式访问时则出错，在访问 Redis 以及之后的代码均不再执行。查看 Apache 的日志：`/var/log/httpd/error_log`，发现代码运行时出现异常：
``` bash
[root@localhost ~]# cat /var/log/httpd/error_log
[Tue May 10 10:24:06.720875 2016] [:error] [pid 5449] [client 10.0.2.2:62045] PHP Fatal error:  Uncaught exception 'RedisException' with message 'Redis server went away' in /var/www/html/redis_test.php:4\nStack trace:\n#0 /var/www/html/redis_test.php(4): Redis->set('domain', 'www.dear521.com')\n#1 {main}\n  thrown in /var/www/html/redis_test.php on line 4
```

在命令行可以正常运行说明编码肯定没有问题，在网上搜索了一下，说是 php 的 Sokcet 超时时间设置的过短,在文件开头应该加上如下代码
``` php
ini_set(‘default_socket_timeout’, -1);
```
经过测试好像并没起什么作用，这行代码只是设置 php 的 sokcet 连接永不超时。

# selinux

关于 selinux :大部分使用 SELinux 的人使用的都是 SELinux 就绪的发行版。
例如：Fedora、RedHatEnterpriseLinux(RHEL）、Debian或Centos。

猜测应该是selinux在搞鬼，查看selinux的状态
``` bash
[root@localhost ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28
```

现在的模式是 `enforcing` ,只要违反安全策略就不想下继续执行

修改selinux模式（这只是临时修改）
``` bash
[root@localhost ~]# setenforce 0
```
关于永久修改，可以在本博客中查看 selinux 的相关标签。

测试结果
![test](/img/201603/redis_php/test.jpg)

好了，问题解决。

对于平时学习来说，推荐关闭 selinux ,我可不想在学习中防来防去却防到了自己。





