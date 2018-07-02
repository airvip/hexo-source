---
title: Ubuntu16.04 安装Teamviewer
date: 2018-07-02 16:19:55
tags: 
    - Ubuntu
    - Teamviewer
---

> 我只是做了我能做的事，没有时间想将来。

对于生活中离不开远程工作的人来说，TeamViewer 是非常不错的选择，并且 TeamViewer 支持的操作系统特别多，如：Windows,Mac,Linux,Chrome OS,Android,ios 等等，这么优秀的软件怎么能不安装。

<!-- more -->

官网对 Linux 版 TeamViewer 的解说

建立入站和出站远程连接，实现对其他计算机的实时支持或访问。参加会议和演示，与其他人或群组聊天，并进行视频通话。在下载和安装该软件后，您将在数秒内建立并运行首个会话。

# 安装流程

1、下载相应 linux 版本的 Teamviewer

官网下载地址 ：`https://www.teamviewer.com/zhcn/download/linux/`, 我的操作系统是 Ubuntu16.04 64位,我就下载软件包 teamviewer_amd64.deb

``` Bash
$ wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
```

2、快速安装

毫无疑问我们直接安装会缺少很多软件依赖,我们稍后修复依赖

``` Bash
$ sudo dpkg -i install teamviewer_amd64.deb
```

![install_teamview](/img/201807/teamviewer/install_teamview.png)

3、解决缺少的依赖

``` Bash
$ sudo apt install -f
```

4、依赖安装完成之后，我们在 dash 搜索 teamviewer，然后打开就可以了

![teamview](/img/201807/teamviewer/teamview.png)


# 卸载 Teamviewer

```
$ sudo apt purge teamviewer
```

如果无法卸载，请使用下面的命令

```
$ sudo dpkg -r --force teamviewer
$ sudo rm -r ~/.local/share/teamviewer11
$ sudo rm -r ~/.config/teamviewer
$ sudo rm -r /etc/teamviewer
$ sudo apt-get update
$ sudo apt-get dist-upgrade
```
