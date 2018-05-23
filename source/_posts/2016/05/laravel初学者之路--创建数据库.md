---
title: laravel初学者之路--创建数据库
date: 2016-05-16 14:31:49
tags:
    - Php
    - Laravel
---

> 只要微笑就可以了。

laravel初学者之路将由laravel5及之后的版本作为学习目标，这里只是简单的记录一下学习过程，有兴趣的朋友可以一起学习。正所谓：“三人行，必有我师焉”。

<!-- more -->

在每开一个项目的时候，一定要做数据字典，有了数据字典，在之后的很多操作中，将会省去很大一部分不必要的操作，例如：查看表字段名叫什么？

# 创建数据库

方法一：

通过建模工具创建数据库，之所以选择建模工具，也是为了快速完成需求。

传送门：建模工具数据库管理神器mysql workbench

![mysql workbench](/img/201605/laraveldb/workbench.jpg)

建立好数据库之后就是导入数据库了。

方法二：

既然是学习laravel框架，当然要使用laravel提供的功能来创建数据库了。正所谓：为尽其用嘛！

1. 进入数据库创建一个自己使用的数据库
``` bash
C:\Users\wzb>mysql -h127.0.0.1 -uroot -p
Enter password: ***
mysql> create database self;
//之所以use一下是为了之后看效果
mysql> use self;
```

2. 先配置一下laravel的基础环境
``` bash
//总会要使用吧，配置一下啦！文件位置：项目目录/config/databases.php
//这是默认的，反正我也要使用mysql
'default' => env('DB_CONNECTION', 'mysql'),
//配置一下数据库地址、端口、数据库名[self]、用户[root]、密码[***],根据自己情况
'mysql' => [
	'driver' => 'mysql',
	'host' => env('DB_HOST', 'localhost'),
	'port' => env('DB_PORT', '3306'),
	'database' => env('DB_DATABASE', 'self'),
	'username' => env('DB_USERNAME', 'root'),
	'password' => env('DB_PASSWORD', '***'),
	'charset' => 'utf8',
	'collation' => 'utf8_unicode_ci',
	'prefix' => '',
	'strict' => false,
	'engine' => null,
],
//文件位置：项目目录/.env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=self
DB_USERNAME=root
DB_PASSWORD=***
```

3. 重新打开一个cmd，进入你的项目目录
推荐一款好用php的IDE,之所以好用是因为在底部的terminal可以完成你在cmd的大量操作。
``` bash
//我的项目在d盘
C:\Users\wzb>d:
//self是我的项目目录
D:\>cd download/apache/htdocs/self
//创建表文件
D:\download\apache\htdocs\self>php artisan migrate:make create-users-table
//在你的项目目录/databases/migrations/下多了点文件
//编辑2016_05_16_023000_create_users_table.php，之前的这个文件被我删了，你可以直接修改
public function up()
{
    Schema::create('users', function (Blueprint $table) {
        $table->increments('id');
        $table->string('nickname',20)->unique();
        $table->string('email',100)->unique();
        $table->char('password',32);
        $table->rememberToken();
        $table->timestamps();
    });
}

public function down()
{
    Schema::drop('users');
}
```

4. 在你一个个重复第3步把数据表文件都建好了，在命令行（cmd）中执行
``` bash
D:\download\apache\htdocs\self>php artisan migrate
```

5. 查看数据库
``` bash
mysql> show tables;
+----------------+
| Tables_in_self |
+----------------+
| migrations     |
| users          |
+----------------+
2 rows in set (0.00 sec)
```

到此数据库建好了。

关于更多的laravel数据库迁移（migration）的命令请查看手册
``` bash
//简单列一下
 migrate
 migrate:refresh
 migrate:status
 migrate:reset
 migrate:rollback
 migrate:install
 make:migration
```