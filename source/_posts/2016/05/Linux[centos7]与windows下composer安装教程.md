---
title: Linux[centos7]与windows下composer安装教程
date: 2016-05-02 12:11:09
tags:
    - Composer
---

> 我最喜欢的人就是哥哥了！

Composer 是 PHP 的一个依赖管理工具。它允许你申明项目所依赖的代码库，它会在你的项目中为你安装他们。

<!-- more -->

Composer 不是一个包管理器。是的，它涉及 "packages" 和 "libraries"，但它在每个项目的基础上进行管理，在你项目的某个目录中（例如 vendor）进行安装。默认情况下它不会在全局安装任何东西。因此，这仅仅是一个依赖管理。

composer就像node的npm， ruby的bundler，咱们php也不能落后啊! composer的作用就发挥出来了。当然，早年间 PHP 其实是有自己的包管理器的，叫 pear。不想多讲，知道pear很垃圾就好了。

# Composer用途

Composer 这样为你解决问题：
1. 你有一个项目依赖于若干个库。
2. 其中一些库依赖于其他库。
3. 你声明你所依赖的东西。
4. Composer 会找出哪个版本的包需要安装，并安装它们（将它们下载到你的项目中）。

**注意**：运行 Composer 需要 PHP 5.3.2+ 以上版本。一些敏感的 PHP 设置和编译标志也是必须的，但对于任何不兼容项安装程序都会抛出警告。

话不多说，现在安装

# Linux（CentOs7）下安装

1、下载并执行 Installer
``` bash
[root@localhost ~]# curl -sS https://getcomposer.org/installer | php
All settings correct for using Composer
Downloading 1.1.0...
Composer successfully installed to: /root/composer.phar
Use it: php composer.phar
```

2、切换到全局安装文件夹
``` bash
[root@localhost ~]# mv composer.phar /usr/local/bin/composer
```
注意： 如果上诉命令因为权限执行失败， 请使用 sudo 再次尝试运行 mv 那行命令。

3、进入全局安装目录，看看权限
``` bash
[root@localhost ~]# cd /usr/local/bin/
[root@localhost bin]# ls -l
-rwxr-xr-x. 1 root root  1639528 May 14 02:30 composer
```
现在只需要运行 composer 命令就可以使用 Composer 而不需要输入 php composer.phar

4、升级composer(为以后做准备)
``` bash
[root@localhost ~]# composer self-update
//谁叫咱是新安装的呢
You are already using composer version 1.1.0 (stable channel).
```

# windows下安装

使用安装程序，这是将 Composer 安装在你机器上的最简单的方法。

传送门：[现在就去下载composer安装包](https://getcomposer.org/download/)

进入传送门后，对比下面的图片，**点击**红框位置将直接下载
![down composer](https://s2.ax1x.com/2020/02/06/1yJ5qg.jpg)

安装好之后在cmd更新一下
``` bsah
C:\>composer self-update
//我的之所以会更新是因为我之前的版本比较老了，如果是刚安装的就不会更新了，前提是你安装的是最新稳定版
Updating to version 1.1.0.
    Downloading: 100%
Use composer self-update --rollback to return to version b2b46032154d21670e9ae3e
84bb92efdf3522d02
```