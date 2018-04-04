---
title: ubuntu16.04使用systemctl管理redis
date: 2018-03-13 10:12:43
tags: 
    - Linux
    - Ubuntu
    - Redis
---

> 每一个人都应该明确自己的方向和位置。

现在流行的 Linux 发行版中都默认集成 systemd 功能，使用 systemd 来管理服务的启动、停止、设置开机自启等功能都非常方便，现在我们也对我们的 redis 配置下。

<!-- more -->

## 操作环境
``` bash
$ uname -a # 系统版本
Linux airvip-ubuntu 4.13.0-37-generic #42~16.04.1-Ubuntu SMP Wed Mar 7 16:03:28 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
$ redis-server --version # redis服务端版本
Redis server v=3.0.6 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 build=687a2a319020fa42
$ redis-cli --version # redis客户端版本
redis-cli 3.0.6
```

## 安装方式

redis安装方式
``` bash
$ sudo apt-get -y install redis-server
$ sudo systemctl status redis-server
$ sudo systemctl status redis # 这个也可以
```
安装之后默认已启动服务，并且开机自启，也可以使用 systemd 来管理服务。

想要不用开机自启，我们执行下面操作
``` bash
$ sudo systemctl stop redis
$ sudo systemctl disable redis-server # 开机不自启
Synchronizing state of redis-server.service with SysV init with /lib/systemd/systemd-sysv-install...
Executing /lib/systemd/systemd-sysv-install disable redis-server
insserv: warning: current start runlevel(s) (empty) of script `redis-server' overrides LSB defaults (2 3 4 5).
insserv: warning: current stop runlevel(s) (0 1 2 3 4 5 6) of script `redis-server' overrides LSB defaults (0 1 6).
Removed symlink /etc/systemd/system/redis.service.
```
不难发现我们执行了`sudo systemctl disable redis-server`之后，删除了一个符号链接(软连接)。其实还删除了`/etc/systemd/system/redis.service`这个符号链接文件的`/etc/systemd/system/multi-user.target.wants/redis-server.service`符号链接文件。

现在执行`systemctl start redis`不好用了，我们只需要创建一个链接文件即可。
``` bash
$ sudo ln -s /lib/systemd/system/redis-server.service /etc/systemd/system/redis.service
```

## systemctl管理redis-server
在老一点的 linux 版本中用 service 来管理服务的时候，是在 `/etc/init.d/` 目录中创建一个脚本文件，来管理服务的启动和停止，其实用 systemctl 来管理也类似，只是文件目录有所不同，在 `/lib/systemd/system` 下。

`/lib/systemd/system/redis-server.service` 文件内容如下
``` bash
[Unit] # 表示这是基础信息
Description=Advanced key-value store # 描述说明
After=network.target # 是在哪个服务后面启动，一般是网络服务启动后启动
Documentation=http://redis.io/documentation, man:redis-server(1) # 文档说明

[Service] # 服务信息 
Type=forking # 服务类型，常用 simple(默认类型) 和 forking
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf # 启动服务的命令配置
PIDFile=/var/run/redis/redis-server.pid
TimeoutStopSec=0
Restart=always
User=redis
Group=redis

ExecStartPre=-/bin/run-parts --verbose /etc/redis/redis-server.pre-up.d
ExecStartPost=-/bin/run-parts --verbose /etc/redis/redis-server.post-up.d 
ExecStop=-/bin/run-parts --verbose /etc/redis/redis-server.pre-down.d # 停止服务指令配置
ExecStop=/bin/kill -s TERM $MAINPID # 停止服务指令配置
ExecStopPost=-/bin/run-parts --verbose /etc/redis/redis-server.post-down.d

PrivateTmp=yes
PrivateDevices=yes
ProtectHome=yes
ReadOnlyDirectories=/
ReadWriteDirectories=-/var/lib/redis
ReadWriteDirectories=-/var/log/redis
ReadWriteDirectories=-/var/run/redis
CapabilityBoundingSet=~CAP_SYS_PTRACE

# redis-server writes its own config file when in cluster mode so we allow
# writing there (NB. ProtectSystem=true over ProtectSystem=full)
ProtectSystem=true
ReadWriteDirectories=-/etc/redis

[Install] # 安装信息相关
WantedBy=multi-user.target # 方式启动：multi-user.target表明当系统以多用户方式（默认的运行级别）启动时，这个服务需要被自动运行。
Alias=redis.service # 服务别名
```






