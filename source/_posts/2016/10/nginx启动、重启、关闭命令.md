---
title: nginx启动、重启、关闭命令
date: 2016-10-26 12:00:00
tags:
    - Nginx
---

> 只要能努力，就应当去努力；只要还能前进，就要向前走去。

Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。

<!-- more -->

绝大多数的 Nginx 运行在 Linux 机器上, 当然也有运行在windows机器上的. 所以本文将以 Linux 和windows为例讲解, 而其他 Unix like 机器参考Linux的命令.

* 知道安装目录很重要，即便大家默认安装软件也要仔细看好软件的安装在哪个目录

# Linux中的Nginx

以阿里云web一键安装包为例

1、启动

``` Bash
# /alidata/server/nginx/sbin/nginx
```

2、重启

``` Bash
# /alidata/server/nginx/sbin/nginx -s reload
```

或者使用 `kill -HUP 主进程号或进程号文件路径`
主进程号查看命令 `ps -ef | grep nginx`

3、停止
``` Bash
# /alidata/server/nginx/sbin/nginx -s stop
```

或者使用以下命令杀掉

从容停止   `kill -QUIT 主进程号`
快速停止   `kill -TERM 主进程号`
强制停止   `kill -9 nginx`

若nginx.conf配置了pid文件路径，如果没有，则在logs目录下
`kill -信号类型 '/alidata/server/nginx/logs/nginx.pid'`

4、升级

4.1、先用新程序替换旧程序文件
4.2、kill -USR2 旧版程序的主进程号或者进程文件名
　　此时旧的nginx主进程会把自己的进程文件改名为.oldbin，然后执行新版nginx，此时新旧版本同时运行
4.3、kill -WINCH 旧版本主进程号
4.4、不重载配置启动新/旧工作进程
　　kill -HUP 旧/新版本主进程号
　　从容关闭旧/新进程
　　kill -QUIT 旧/新进程号
　　快速关闭旧/新进程
　　kill -TERM 旧/新进程号

# Windows中的Nginx

以vbox中的虚拟机为例，如果想方便以后操作，最好把nginx安装目录的nginx加入到环境变量

1、启动

``` Bash
D:\nginx>start nginx
```

2、重启

``` Bash
D:\nginx>nginx -s reload
```

3、停止

``` Bash
D:\nginx>nginx -s quit
D:\nginx>nginx -s stop
```

注：quit是完整有序的停止nginx，并保存相关信息；stop是快速停止nginx，可能并不保存相关信息；

4、查看版本

``` Bash
D:\nginx>nginx -v
```

查看windows任务管理器下Nginx的进程命令

``` Bash
D:\nginx>tasklist /fi "imagename eq nginx.exe
```

5、重新打开日志文件

``` Bash
D:\nginx>nginx -s reopen
```