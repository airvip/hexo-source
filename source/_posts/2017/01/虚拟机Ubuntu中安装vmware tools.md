---
title: 虚拟机ubuntu中安装vmware tools
date: 2017-01-31 10:42:50
tags: 
    - Ubuntu
    - 虚拟机
---

> 风筝的线你随时可以放开，只是别盼望我会回来。

vmware tools是虚拟机VMware Workstation自带的一款工具。我认为vmware tools 最讨喜的一点就是能够在虚拟机与物理主机之前共享文件。

<!-- more -->

## vmware tools 功能

1. 更新虚拟机中的显卡驱动, 使虚拟机中的XWindows可以运行在SVGA模式下。
2. 在主机和客户机之间时间同步。
3. 支持同一个分区的真实启动和从虚拟机中启动, 自动修改相应的设置文件。
4. 自动捕获和释放鼠标光标。未安装VMware Tools的时候只能用Ctrl+Alt来释放鼠标，安装VMware Tools后可以实现虚拟机和主机图形用户界面之间平滑移动鼠标光标。
5. 在主机和客户机之间或者从一台虚拟机到另一台虚拟机进行复制和粘贴操作。
6. 改善网络性能。
...等等优秀的功能。

## vmware tools 安装

虚拟机主要是以 Ubuntu16.04 Linux 版本为演示目标。

1. 开启虚拟机，在菜单栏中找到  虚拟机>安装VMware tools 或者是 重新安装VMware tools
![vmtools](/img/201701/vmtools/vmtools01.png)
  在 Ubuntu 启动器中有个 DVD 字样的图标，鼠标放在上面显示 VMware tools 的字样，说明虚拟 CD 驱动器已经挂载到咱们的虚拟客户机中了。

2. 启动终端 terminal, 依次执行下面的命令
``` bash
$ cd /media/airvip/VMware\ Tools  #airvip 目录是当前用户名
$ ls
manifest.txt     VMwareTools-10.2.0-7259539.tar.gz  vmware-tools-upgrader-64
run_upgrader.sh  vmware-tools-upgrader-32
``

3. 将 VMwareTools-10.2.0-7259539.tar.gz 解压到个人主目录下
``` bash
$ tag -zxvf VMwareTools-10.2.0-7259539.tar.gz ~/
```

4. 进入加压目录进行安装
``` bash
$ cd ~
$ cd vmware-tools-distrib/
$ ls #查看文件
bin  caf  doc  etc  FILES  INSTALL  installer  lib  vgauth  vmware-install.pl
$ ./vmware-install.pl#进行安装
```
  一路按回车键就可以。

5. 重启系统是配置生效
``` bash
$ reboot
```
