---
title: docker login 提示 Error saving credentials
date: 2020-11-12 11:50:34
tags: 
    - Docker
    - Ubuntu
---

> 如果多次想要放弃，不妨多看看自己的荷包。

今天在 Vbox 内的 Ubuntu 上登录 docker， 结果登录时提示存储证书错误，随笔记录解决办法

<!-- more -->

## 环境

```
root@airvip:~# docker --version
Docker version 19.03.13, build 4484c46d9d

root@airvip:~# hostnamectl
   Static hostname: airvip
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 27f68799070f446db951db372b1aa27a
           Boot ID: 34a555ce4e3c4f9e88f24c3a6bf748ca
    Virtualization: oracle
  Operating System: Ubuntu 18.04.4 LTS
            Kernel: Linux 4.15.0-99-generic
      Architecture: x86-64
```

## 问题

```
Error saving credentials: error storing credentials - err: exit status 1, out: `Cannot autolaunch D-Bus without X11 $DISPLAY`
```

登录操作

```
root@airvip:~# docker login -u xxxx@xxxx.com ynhdkcdyt-docker.pkg.coding.net
Password: 
Error saving credentials: error storing credentials - err: exit status 1, out: `Cannot autolaunch D-Bus without X11 $DISPLAY`
```

## 解决

```
root@airvip:~# apt -y install gnupg2 pass
```

* [gnupg2](https://launchpad.net/ubuntu/+source/gnupg2): 一种包含数字签名和证书的加密工具。
* [pass](http://manpages.ubuntu.com/manpages/trusty/man1/pass.1.html): 一种简单的密码管理工具。

安装好之后在重新登陆即可

## 参考

[参考 Github issue 资料](https://github.com/docker/cli/issues/1136)