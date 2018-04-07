---
title: Centos7下laravel安装初体验
date: 2016-05-07 14:05:31
tags:
    - Php
    - Laravel
---

Laravel 是一套简洁、优雅的PHP Web开发框架(PHP Web Framework)。它可以让你从面条一样杂乱的代码中解脱出来;它可以帮你构建一个完美的网络APP。

<!-- more -->

## 安装环境
安装laravel环境要求(来自官网)

* PHP >= 5.5.9 // php版本要求大于等于5.5.9 ，我的是5.4.16也安装好了。。。。
* OpenSSL PHP Extension // 开启openssl 扩展
* PDO PHP Extension // 开启pdo扩展
* Mbstring PHP Extension // 开启mbstring扩展
* Tokenizer PHP Extension // 开启tokenizer扩展

[laravel安装详细教程](https://laravel.com/docs/5.2#installation)

**本机php版本**
``` bash
[root@localhost html]# php -v
PHP 5.4.16 (cli) (built: May 12 2016 13:45:17) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
```

## 安装laravel

通过composer安装,使用 `composer create-project laravel/laravel 项目名称` 来创建项目
``` bash
[root@localhost html]# composer create-project laravel/laravel self
Installing laravel/laravel (v5.0.22)
  - Installing laravel/laravel (v5.0.22)
    Loading from cache
Created project in self
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Your requirements could not be resolved to an installable set of packages.
  Problem 1
    - Installation request for laravel/framework v5.0.16 -> satisfiable by laravel/framework[v5.0.16].
    - laravel/framework v5.0.16 requires ext-mcrypt * -> the requested PHP extension mcrypt is missing from your system.
```
说好的扩展都开启了，这是什么情况？？？<(￣︶￣)>好吧！先安装php-mcrypt扩展

先看软件源里有木有
``` bash
[root@localhost html]# yum list | grep php-mcrypt
php-mcrypt.x86_64                       5.4.16-4.el7                   epel 
```

安装php-mcrypt扩展
``` bash
[root@localhost html]# yum -y install php-mcrypt.x86_64
```
![php-mcrypt](/img/201605/centos_laravel/php-mcrypt.png)

编集php.ini（我的不需要编辑）
``` bash
extension=mcrypt.so
//有;extension=mcrypt.so的去掉; 没有的直接添加extension=mcrypt.so
```

重启服务器
``` bash
[root@localhost html]# systemctl restart httpd
```

安装laravel框架创建项目
``` bash
[root@localhost html]# composer create-project laravel/laravel self
Installing laravel/laravel (v5.0.22)
  - Installing laravel/laravel (v5.0.22)
    Loading from cache
Created project in self
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
  - Installing jakub-onderka/php-console-color (0.1)
    Downloading: 100%         
。。。。。省略很多。。。。。
 - Installing phpunit/php-code-coverage (2.0.15)
    Downloading: 100%         
  - Installing phpunit/phpunit (4.5.0)
    Downloading: 100%  
```
## 成功测试
安装成功可以看到如下代码
``` bash
Generating autoload files
> php artisan clear-compiled
> php artisan optimize
Generating optimized class loader
Compiling common classes
> php -r "copy('.env.example', '.env');"
> php artisan key:generate
Application key [mSRzuSWOKR0AfSAJqg6hYsYrX4TUvrk6] set successfully.
```
![laravel5](/img/201605/centos_laravel/laravel5.png)