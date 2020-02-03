---
title: Linux(centos7)下yum安装redis
date: 2016-02-08 15:05:15
tags:
    - Linux
    - Centos
---

> 我觉得只要这样继续加油，总有一天能赶上他们的。

Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。

<!-- more -->

EPEL 是yum的一个软件源,里面包含了许多基本源里没有的软件。epel是社区强烈打造的免费开源发行软件包版本库，系统包含大概有1万多个软件包，163和sohu的镜像是没有这么多软件了.

# 添加源并安装
```
[root@localhost ~]# yum install epel-release
```

查看之前有没有安装redis。(确实没装)
```
[root@localhost ~]# yum list installed | grep redis
```

查看yum源里的redis有木有
```
[root@localhost ~]# yum list | grep redis
redis.x86_64                            2.8.19-2.el7                   epel 
```

安装redis
```
[root@localhost ~]# yum -y install redis
```
![redis_install.jpg](https://s2.ax1x.com/2020/02/03/1UEpDI.jpg)
安装完可以使用`rpm -ql redis`查看安装的位置


# 管理redis

启动redis服务
```
[root@localhost ~]# systemctl start redis.service
```

查看redis进程
```
[root@localhost ~]# ps -A | grep redis
 2825 ?        00:00:00 redis-server
```

将redis加入开机启动
```
[root@localhost ~]# systemctl enable redis.service
ln -s '/usr/lib/systemd/system/redis.service' '/etc/systemd/system/multi-user.target.wants/redis.service'
```

查看redis状态
```
[root@localhost ~]# systemctl status redis.service
```
![redis_status.jpg](https://s2.ax1x.com/2020/02/03/1UESKA.jpg)

* enable已经开启随即启动，avtive(running)正在运行

附加命令（centos7已经使用systemd管理器）
```
重启redis服务
[root@localhost ~]# systemctl restart redis.service
停止redis服务
[root@localhost ~]# systemctl stop redis.service
关闭开机自启动
[root@localhost ~]# systemctl disable redis.service
```
至此yum安装已经结束

# 编译安装

截至2016/02/08，最新稳定版为3.2.0
```
[root@localhost ~]# wget https://github.com/antirez/redis/archive/3.2.0-rc3.tar.gz
```
或者直接进入官网下载，在上传到linux的某一目录

解压
```
[root@localhost ~]# tar -zxvf redis-3.2.0-rc3.tar.gz 
```

进入解压的目录，编译安装
```
[root@localhost ~]# cd redis-3.2.0-rc3
[root@localhost ~]# make
```

设置配置文件路径:
```
# mkdir -p /etc/redis && cp redis.conf /etc/redis
```

修改配置文件：
```
# vi /etc/redis/redis.conf
修改： daemonize yes
```

启动Redis:
```
# /usr/local/bin/redis-server /etc/redis/redis.conf
```

[官网文档编译安装教程传送门(在底部)](http://redis.io/download)