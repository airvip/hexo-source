---
title: laravel初学者之路--bower管理前端依赖包
date: 2016-05-16 16:52:32
tags:
    - Php
    - Laravel
    - Bower
---

> 重要的不是你做了多少事，而是你放了多少心思进去。

laravel初学者之路将由laravel5及之后的版本作为学习目标，这里只是简单的记录一下学习过程，有兴趣的朋友可以一起学习。正所谓：“三人行，必有我师焉”。

<!-- more -->

Bower是一个客户端技术的软件包管理器,它可用于搜索、安装和卸载如JavaScript、HTML、CSS之类的网络资源。

对于没有UI功底的程序员来说，选择一款好的前端框架能达到事半功倍的效果，现在我们使用bootstrap，来做我们的前端框架。

# bower 管理依赖

1、找到项目，进入public目录
``` bash
//我的项目在d盘
C:\Users\wzb>d:
//self是我的项目目录,并进入public目录
D:\>cd download/apache/htdocs/self/public
```

2、安装Bootstrap
``` bash
D:\download\apache\htdocs\self\public>bower install bootstrap
```
![install bootstrap](https://s2.ax1x.com/2020/02/06/1yJDqe.jpg)
从上图可以看出我们的bootstarp3.3.6已经安装好了，并且jquery2.2.3也安装好了

3、检测结果
在项目的public目录我们可以看到多了bower_components目录，在这个目录里面有我们刚刚安装的bootstarp以及jquery
![install bootstrap](https://s2.ax1x.com/2020/02/03/1UKW6O.jpg)

# 关于bower的安装

bower安装环境需要node、npm、git（来自bower的官网）
bower是一个命令行实用程序。通过npm安装。

**windows下安装**
``` bash
D:\download>npm install -g bower
# 查看bower的版本
D:\download>bower --version
1.7.9
```

**linux下安装**
``` bash
[root@localhost ~]#npm install -g bower
```

使用bower命令来安装依赖包
``` bash
# 通过bower install安装依赖包，安装的包会放在bower_components目录下
$ bower install <package>
# 安装bower.json上的项目依赖
$ bower install
# 安装注册过的包
$ bower install jquery
# 安装GitHub简称包
$ bower install desandro/masonry
# 安装Git源里的包
$ bower install git://github.com/user/package.git
# 通过url安装包
$ bower install http://example.com/script.js
```

相关链接；
[bower 官网](http://bower.io/)
[搜索在bower上注册过的包](http://bower.io/search/)

