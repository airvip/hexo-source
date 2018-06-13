---
title: Apache基于同一个ip配置虚拟主机
date: 2016-06-23 13:52:02
tags:
    - Apache
---

> 如果爱请深爱，不爱请放开，拖着对彼此都是伤害。

解决自己有多个域名及子域名（多个站点），只有一个公网IP的问题。大家可以通过将子域名或多个域名直接解析到同一个IP上就好。

<!-- more -->

# 本地测试

1、修改hosts文件

```
127.0.0.1 localhost
127.0.0.1 myshop.com
127.0.0.1 www.myshop.com
127.0.0.1 myblog.com
127.0.0.1 www.myblog.com
127.0.0.1 mydb.com
127.0.0.1 www.mydb.com
```

2、修改 https.conf 文件

在 httpd.conf 文件中将附加配置文件 httpd-vhosts.conf 包含进来。
即：在 httpd.conf 中写入如下配置，如果存在，前面有 # 号，直接去掉 # 就可以。

```
# Virtual hosts
Include conf/extra/httpd-vhosts.conf
```

3、conf/extra/httpd-vhosts.conf 文件修改如下

```
<VirtualHost *:80>
    ServerAdmin sdqhwzb@163.com
    DocumentRoot "D:\download\apache\htdocs\shopa"
    ServerName www.myshop.com
    ServerAlias myshop.com
    ErrorLog "logs/shopa-error.log"
    CustomLog "logs/shopa.log" common
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin sdqhwzb@163.com
    DocumentRoot "D:\download\apache\htdocs\Airblog"
    ServerName www.myblog.com
    ServerAlias myblog.com
    ErrorLog "logs/airblog-error.log"
    CustomLog "logs/airblog.log" common
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin sdqhwzb@163.com
    DocumentRoot "D:\download\apache\htdocs\phpmyadmin"
    ServerName www.mydb.com
    ServerAlias mydb.com
    ErrorLog "logs/mydb-error.log"
    CustomLog "logs/mydb.log" common
</VirtualHost>
```

**说明**

ServerAdmin：可以不写，因为apache官方给的有，我就添加上了。

ServerAdmin sdqhwzb@163.com  你的地址如果服务器有任何问题将发信到这个地址， 这个地址会在服务器产生的某些页面中出现，例如，错误报告