---
title: 使用xshell连接win10的ubuntu子系统
date: 2019-07-22 10:11:54
tags: 
    - SSH
    - WSL
---

> "我退了，这一退就是一辈子"

实在无法忍受一个个的打开 Bash，还是喜欢 xshell 可以开多个标签页，决定使用 xshell 连接子系统。

<!-- more  -->

不管装没装 ssh ,直接暴力的重新来一遍

1 先删除 ssh

```
$ sudo apt-get remove --purge openssh-server
```

2 卸载完毕，重新安装 ssh

```
$ sudo apt-get install openssh-server
```

3 修改 sshd 配置(可以跳过该步骤)

```
$ cd /etc/ssh
$ sudo cp sshd_config sshd_config.bak # 备份
$ sudo vim sshd_config
```

修改内容如下

```
Port 2222  # 修改端口
ListenAddress 0.0.0.0  # 打开本地监听
#StrictModes yes  #注释掉
PasswordAuthentication yes  #允许密码登陆
```

4 启动 ssh

```
$ sudo service ssh restart
```

现在就可以使用xshell登录了，效果如下

![xshell_login](https://s2.ax1x.com/2020/01/13/l75RCF.png)

* 几个操作ssh的命令

```
$ sudo service ssh --full-restart
$ sudo service ssh stop
$ sudo service ssh start
$ sudo service ssh restart
```

**说明**

* 关了bash后xshell会断线
