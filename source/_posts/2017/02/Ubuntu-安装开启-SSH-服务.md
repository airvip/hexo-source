---
title: Ubuntu 安装开启 SSH 服务
date: 2017-02-02 00:34:03
tags:
    - Ubuntu
    - SSH
---

> 每一个人都应该明确自己的方向和位置。

SSH 为 Secure Shell 的缩写，由 IETF 的网络小组（Network Working Group）所制定；SSH 为建立在应用层基础上的安全协议。

<!-- more -->

SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。

Ubuntu 默认并没有安装 ssh 服务，如果通过 ssh 链接 ubuntu，需要自己手动安装 ssh-server。

# 安装 ssh-server

1. 检测是否安装 ssh server 软件包
``` bash
$ dpkg -l | grep openssh-server
```
  没有列出软件包，说明没有安装。
  或者使用
``` bash
$ dpkg -s openssh-server
```

2. 更新软件源（安装软件前的习惯性操作）
``` bash
$ sudo apt update
```

3. 安装 ssh server 软件包
``` bash
$ sudo apt -y install openssh-server
```

# 启动与关闭 ssh 服务

* 检测 ssh 服务是否启动
``` bash
$ systemctl status sshd
```
  或者使用
``` bash
$ ps -e | grep sshd
  7048 ?        00:00:00 sshd #说明已经启动
```

* 停止 ssh 服务
``` bash
$ systemctl stop sshd
```
  或者使用
``` bash
$ /etc/init.d/ssh stop
[....] Stopping ssh (via systemctl): ssh.service
. ok
```

* 启动 ssh 服务
``` bash
$ systemctl start sshd
```
  或者使用
``` bash
$ /etc/init.d/ssh start
[ ok ] Starting ssh (via systemctl): ssh.service.
```

* 重新启动 ssh 服务
``` bash
$ systemctl restart sshd
```
  或者使用
``` bash
$ /etc/init.d/ssh restart
[ ok ] Restarting ssh (via systemctl): ssh.service.
```

* 重新加载 ssh 服务配置
``` bash
$ systemctl reload sshd
```
  或者使用
``` bash
$ /etc/init.d/ssh reload
[ ok ] Reloading ssh configuration (via systemctl): ssh.service.
```

# 加入开启启动项

1. 查看是否已经加入开机启动项
![ssh-enable](https://s3.ax1x.com/2021/01/13/sNZj6x.png)
  active(running)运行着呢！ enable加入了开机启动项

2. 如何加入开机启动项呢
``` bash
$ systemctl enable ssh.service
```
