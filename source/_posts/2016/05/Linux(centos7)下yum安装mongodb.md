---
title: Linux(centos7)下yum安装mongodb
date: 2016-05-28 15:41:25
tags:
    - Mongodb
    - Linux
    - Centos
---

> 美好的人眼裡映出的世界也是美好的。

为什么采用yum安装？有很多学习的朋友，linux是安装在虚拟机中的，如果编译安装，可想而知是多么的浪费时间！并且yum安装可以解决报依赖问题，所以没有理由不选择yum安装。

<!-- more -->

## 操作环境

先查看当前的操作环境
``` bash
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-229.el7.x86_64 #1 
SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 7.1.1503 (Core) 
```

## 安装Mongodb

查看是否安装mongodb
``` bash
[root@localhost ~]# yum list installed | grep mongodb
```

查看yum库有没有mongodb
``` bash
[root@localhost ~]# yum list | grep mongodb
mongodb.x86_64                          2.6.11-1.el7                   epel     
mongodb-server.x86_64                   2.6.11-1.el7                   epel     
mongodb-test.x86_64                     2.6.11-1.el7                   epel     
nodejs-mongodb.noarch                   1.4.7-1.el7                    epel     
poco-mongodb.x86_64                     1.6.1-2.el7                    epel     
syslog-ng-mongodb.x86_64                3.5.6-3.el7                    epel 
```

开始安装 mongodb 服务端
``` bash
[root@localhost etc]# yum -y install mongodb-server
```

安装结果
![install mongodb server]()

启动mongodb服务
``` bash
[root@localhost ~]# systemctl start mongod.service
```

开始安装 mongodb 客户端
``` bash
[root@localhost ~]# yum -y install mongodb
```
安装结果
![install mongodb client]()

连接mongodb
``` bash
[root@localhost ~]# mongo
MongoDB shell version: 2.6.11
connecting to: test
Welcome to the MongoDB shell.
.....
```

## 相关操作

查看 mongo 进程
``` bash
[root@localhost ~]# ps -A | grep mongo
 3225 ?        00:00:01 mongod
```

将 mongo 加入开机启动项
``` bash
[root@localhost ~]# systemctl enable mongod.service
ln -s '/usr/lib/systemd/system/mongod.service' '/etc/sys
temd/system/multi-user.target.wants/mongod.service'
```

加入开机启动项结果
![with system start]()

active(running)运行着呢！ enable加入了开机启动项

注释
* /var/log/mongodb/mongod.log  日志文件
* /var/lib/mongodb    数据文件
* /etc/mongod.conf    配置文件


