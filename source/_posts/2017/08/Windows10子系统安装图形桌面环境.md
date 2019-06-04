---
title: Windows10子系统安装图形桌面环境
date: 2017-08-17 17:33:25
tags:
    - Windows
    - Ubuntu
---

使用 Linux 的图形用户界面通常有两种方法，一种是通过本机远程桌面连接，另一种是使用 X-Windows 安装使用图形化界面。

<!-- more -->

在给子系统安装了图形桌面后，非常有利于初学者学习使用 Linux。虽然我们有了图形桌面，但还是推荐大家使用小黑窗 Terminal 来对 Linux 操作。因为 Linux 目前主要用于服务器系统，我们服务器是不需要这些累赘的图形界面的。

# 远程桌面连接 xfce4

安装子系统的图形桌面，首先还是 `win`键 + `R` 键，输入 bash ,进入子系统的命令行。

**说明** 如果在下面操作安装软件包特别慢的时候，可以把软件包镜像源切换到国内来。

**安装xorg**（包括显卡驱动、图形环境库等等一系列软件包）

```
airvip@airvip:~$ sudo apt-get install -y xorg
```

**安装xfce4**（运行在类Unix操作系统上，提供轻量级桌面环境）

```
airvip@airvip:~$ sudo apt-get install -y xfce4
```

**安装xrdp**（一种开源的远程桌面协议（RDP）服务器）

```
airvip@airvip:~$ sudo apt-get install -y xrdp
```

**配置xrdp**（配置端口）

```
airvip@airvip:~$ sudo sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini
```

向.xsession中写入xfce4-session

```
airvip@airvip:~$ sudo echo xfce4-session >~/.xsession
```

**重启xrdp服务**

```
airvip@airvip:~$ sudo service xrdp restart
```

如果有防火墙，允许即可。
在 Cortana 中搜索远程桌面连接，点击进入，输入 `127.0.0.1:3390`,连接建立后，输入子系统的账户和密码我们就登陆成功，看到Ubuntu 的图形界面了。

![xfce4](/img/201708/desktop/xfce4.jpg)


**说明** 每次远程连接之前都要在终端中启动xrdp，并且窗口不能关闭。

```
airvip@airvip:~$ sudo service xrdp start
```

**对中文的支持**

先更新软件包

```
airvip@airvip:~$ sudo apt upgrade -y
```

安装中文语言包

```
airvip@airvip:~$ sudo apt install -y language-pack-zh-hans language-pack-zh-hans-base
```

设置本地化环境变量

```
airvip@airvip:~$ echo "LANG=zh_CN.UTF-8" >> ~/.profile
```

重新启动 Ubuntu 即可。

# 直接安装图形界面

**安装X-Windows**

可供选择安装的X-Windows有多个：VcXsrv Windows X Server、Xming、Cygwin X Server，我们选用 VcXsrv Windows X Server。
下载地址为：https://sourceforge.net/projects/vcxsrv/

在 Windows10 系统中下载好之后，并进行安装，安装好之后启动 xlaunch，首次启动自动进入设置界面

选择：“one large window”，Display number 设置成 0，其它默认即可（一直下一步）

![display](/img/201708/desktop/display.jpg)

完成后会打开一个 VcXsrv Server 窗口,这个窗口黑漆漆的什么也没有不要担心。

**安装桌面环境**

`win`键 + `R` 键，输入 bash ,进入子系统的命令行,输入以下命令。

```
airvip@airvip:~$ sudo apt-get install -y ubuntu-desktop unity compizconfig-settings-manager
```

继续配置 compiz 窗口管理器，输入以下命令。

```
airvip@airvip:~$ export  DISPLAY=localhost:0
airvip@airvip:~$ ccsm
```

此时，在 VcXsrv Server 窗口中弹出了 ccsm 的配置界面，按下图勾选，最重要的就是 Ubuntu Unity Plugin 选项，我的默认已经勾选，如果你的没有勾选你在勾选的时候会弹出提示框，根据提示框一直点击 Enable 即可完成配置。最后点击 close 关闭 ccsm。

![ccsm](/img/201708/desktop/ccsm.jpg)


**启动compiz** (打开桌面)

```
airvip@airvip:~$ compiz
```

![ubuntu](/img/201708/desktop/ubuntu.jpg)


**说明** 之后启动也是，先启动 xlaunch, 在进入 bash, 启动 compiz





