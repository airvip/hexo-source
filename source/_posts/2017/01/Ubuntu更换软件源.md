---
title: Ubuntu更换软件源
date: 2017-01-26 00:23:08
tags:
    - Ubuntu
    - Linux
---

> 不要因为结束而哭泣，微笑吧，为你的曾经拥有。

目前情况下，处于国内的朋友们使用 Ubuntu 自带的软件源来安装软件速度是可想而知的，所以我们一般在安装了 Ubuntu 系统之后必做的一件事就是更上国内的软件源。

<!-- more -->

# 准备工作

这也是我在修改别人的源码或者文件时经常做的一件事，就是先做备份。
``` bash
$ sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

# 修改软件源

默认大家已经知晓 vim 编辑器的基本使用。

``` bash
$ sudo vim /etc/apt/sources.list
```

修改后示例片段
``` bash
deb http://ubuntu.cn99.com/ubuntu/ xenial-security universe
# deb-src http://security.ubuntu.com/ubuntu xenial-security universe
deb http://ubuntu.cn99.com/ubuntu/ xenial-security multiverse
# deb-src http://security.ubuntu.com/ubuntu xenial-security multiverse
```

# 更新软件源列表
``` bash
$ sudo apt-get update
```
此时你就需要稍微等待一会就可以看到有这么多的软件是可以更新的，当然更不更新，选择权就在你手上了。

# 软件源

1. [阿里巴巴 Ubuntu 镜像站帮助文档](http://mirrors.aliyun.com/help/ubuntu)
2. [网易开源 Ubuntu 镜像站帮助文档](http://mirrors.163.com/.help/ubuntu.html)