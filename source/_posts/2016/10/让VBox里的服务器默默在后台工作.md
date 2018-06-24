---
title: 让VBox里的服务器默默在后台工作
date: 2016-10-03 14:48:23
tags:
    - Virtualbox
---

> 比起有一百个朋友，不如有个比一百人还要重要的真心朋友。

不需要开启virtualbox的GUI,照样开启对虚拟机的管理之旅，事实是：贫，无币置机以运。（就是太穷了，自己的电脑开那么多界面服务没法跑）

<!-- more -->

管理vbox里的主机，需要用到VBoxManage，位于你的安装目录
windows键+r键，输入cmd回车，cd到你的安装目录
这是我的安装目录，查看一下ls（大家的应该是dir查看）

![vbox_dir](/img/201610/vbox/vbox_dir.jpg)

# vbox管理常用命令

1. 查看有哪些虚拟机
``` bash
VBoxManage list vms
```

2. 查看虚拟机的详细配置
``` bash
VBoxManage list vms --long
```

3. 查看运行着的虚拟机
``` bash
VBoxmanage list runningvms
```

4. 开启虚拟机在后台运行
``` bash
VBoxManage startvm 虚拟机名字 -type headless
```

5. 开启虚拟机并开启远程桌面的支持
``` bash
VBoxManage startvm 虚拟机名字 -type vrdp
```

6. 改变虚拟机的远程连接端口，用于多个虚拟机同时运行
``` bash
VBoxManage Controlvm 虚拟机名字 vrdpport 端口号
```

7. 关闭虚拟机
``` bash
VBoxManage Controlvm 虚拟机 acpipowerbutton
```

8. 强制关闭虚拟机
``` bash
VBoxManage controlvm 虚拟机 poweroff
```

最最重要的一条命令
``` bash
VBoxManage --help
```