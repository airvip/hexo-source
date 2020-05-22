---
title: Win7下安装了mysql结果无法正常启动
date: 2016-08-05 16:44:23
tags:
    - Mysql
---

> 值得获取的东西，就在风险的彼岸。

今天升级了phpmyadmin,要求mysql5.5以上的版本，好吧！那就升级数据库吧，安装完了数据库，可是发现无法正常运行，之后也是一些列问题要解决，在此留个笔记

<!-- more -->

# 异常情况

安装完Mysql之后，通过DOS窗口输入net start mysql时，却提示服务名无效，结果如下
![服务名无效](https://s1.ax1x.com/2020/05/22/YL7XdJ.jpg)

# 解决流程

明明安装了 mysql,难道没有安装服务？
那就查看一下系统服务，先按 `windows键+R键`,输入`services.msc`

看了一遍，确实没有，那就安装 mysql 服务,注意要到 mysql 安装的 bin 目录下
``` bash
D:\download\mysql\bin>mysqld --install
```
![安装服务](https://s1.ax1x.com/2020/05/22/YL7OZ4.jpg)

再次查看系统服务，好吧现在有了。
结果启动时，又有来新问题了，Mysql 的 1607 问题，我晕，继续解决，主要是因为咱之前装的 mysql 有些文件没删干净，端口被占用，像mysql安装目录里面的 my.ini,还有注册表里的服务。

开始进行将 Mysql 全部卸载、删除行动

1、卸载当前的Mysql数据库
2、删除安装目录中所有东西，以及原来安装目录中的所有东西

**警告** 原来目录中的 data 文件夹是你的数据库，根据自身情况选择性删除，可以直接移植到你新安装的数据库文件里哦！

3、进入注册表继续删除，先按 `windows键+R键`,输入 `regedit`
``` bash
# 按照下面的目录删除（有就删除没有就不删）
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\Eventlog\Application\MySQL 目录删除  
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\Eventlog\Application\MySQL 目录删除
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application\MySQL 目录删除  
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControl001t\Services\MYSQL 目录 
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControl002\Services\MYSQL 目录 
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MYSQL 目录
```
操作完成后再次重新安装 Mysql，就可以正常启动 Mysql 数据库了。