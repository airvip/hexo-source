---
title: ubuntu16.04搭建lnmp运行环境
date: 2018-03-08 12:20:50
tags: 
    - Linux
    - Ubuntu
---

> 成熟不是人的心变老，是泪在打转还能微笑。

由于硬件配置比较低，我们在安装完软件之后会禁用软件的自启动，如果是作为服务器用可以不禁用自启动，如果是开发工作本可以当需要时在开启。

<!-- more -->

安装环境及列表如下
``` bash
$ uname -a
Linux airvip-ubuntu 4.13.0-37-generic #42~16.04.1-Ubuntu SMP Wed Mar 7 16:03:28 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
# 安装列表
1. nginx 1.10.3
2. php-7.0
3. mysql 5.7.21
```

## 安装nginx
``` bash
$ sudo apt-cache search nginx  # 搜索nginx安装包
nginx - small, powerful, scalable web/proxy server
nginx-common - small, powerful, scalable web/proxy server - common files
nginx-core - nginx web/proxy server (core version)
...
$ sudo apt-get -y install nginx # 安装nginx
$ systemctl status nginx # 查看nginx状态 默认已启动
$ nginx -v # 查看nginx版本
nginx version: nginx/1.10.3 (Ubuntu)
```
## 配置nginx
nginx默认的用户是`www-data`,可以在`/etc/nginx/nginx.conf`下查看。
``` bash
$ head /etc/nginx/nginx.conf # 第一行用户信息
```
如果不是可以修改成`www-data`
``` bash
$ vim /etc/nginx/nginx.conf # 改成www-data
```
为什么设为`www-data`,是因为php7.0默认的用户及用户组是`www-data`，为了和下面统一。
由于我这是开发本，所以我把用户设置为当前用户`airvip`

重启nginx
```
$ sudo systemctl restart nginx 
```


## 安装php7.0
``` bash
$ sudo apt-cache search php7.* # 搜索php7安装包
php7.0 - server-side, HTML-embedded scripting language (metapackage)
php7.0-cgi - server-side, HTML-embedded scripting language (CGI binary)
...
$ sudo apt-get -y install php7.0-fpm php7.0-mysql php7.0-common php7.0-curl php7.0-cli php7.0-mcrypt php7.0-mbstring php7.0-dom php7.0-soap # 安装php7及其扩展
$ systemctl status php7.0-fpm # 查看php-fpm状态 默认已启动
```

## 配置php7.0
打开php.ini配置文件
``` bash
$ sudo vim /etc/php/7.0/fpm/php.ini
```
找到cgi.fix_pathinfo选项，去掉注释`;`，然后将值设置为0,为了安全！
``` bash
cgi.fix_pathinfo=0
```
启用php7.0-mcrypt
``` bash
$ sudo phpenmod mcrypt
```
重新启动php7.0-fpm
```
$ sudo systemctl restart php7.0-fpm
```
php7.0默认的用户及用户组是`www-data`,可以在`/etc/php/7.0/fpm/pool.d/www.conf`下查看。

由于我的是开发本我就将用户及用户修改为当前用户`airivp`,修改结果如下：
![php www conf](/img/201803/ubuntu_lnmp/php_www_conf.png)

## 搭配php与nginx
修改nginx默认站点配置文件
``` bash
# 先将原有的默认配置文件备份（个人习惯）
$ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_bak  
$ sudo vim /etc/nginx/sites-available/default
```
修改结果如下
![default](/img/201803/ubuntu_lnmp/default.png)

修改完之后重启nginx

编写`info.php`
``` bash
$ vim /var/www/html/info.php
```
写入如下内容
``` bash
<?php
    phpinfo();
?>
```
在浏览器访问`http://127.0.0.1/info.php`结果如下，说明配置成功！
![phpinfo](/img/201803/ubuntu_lnmp/phpinfo.png)



## 安装mysql5.7
``` bash
$ sudo apt-cache search mysql-server-5.* # 搜索mysql5服务端安装包
mysql-server - MySQL database server (metapackage depending on the latest version)
mysql-server-5.7 - MySQL database server binaries and system database setup
$ sudo apt-cache search mysql-client-5.* # 搜索mysql5客户端安装包
mysql-client - MySQL database client (metapackage depending on the latest version)
mysql-client-5.7 - MySQL database client binaries
$ sudo apt-get -y install mysql-server-5.7 mysql-client-5.7 # 安装mysql5.7服务器及客户端
# 在安装过程中提示设置mysql的密码
$ systemctl status mysql # 查看mysql状态 默认已启动
```

以查看 mysql 状态为例说明
![mysql_status](/img/201803/ubuntu_lnmp/mysql_status.png)

从上图中我们已经圈选出两处，`active(running)` 表示已经启动  `enabled`表示开机自启

同样我们可以看到 php7.0-fpm, mysql, nginx 都是开机启动，现在我们关闭开机启动
```
$ sudo systemctl disable nginx
$ sudo systemctl disable php7.0-fpm
$ sudo systemctl disable mysql
```