---
title: Linux(centos7)常用的命令
date: 2016-07-06 22:36:22
tags:
    - Centos
---

> 时间是伟大的作家，总会写下完美的结局。

一段时间没有玩，就把我的密码给忘了，唉，所以说，没事做个笔记还是比较好的，现在就把今天用到额一些命令记录下来，万一哪天又忘了呢？

<!-- more -->

## 常用命令

1、添加用户
``` bash
[root@localhost /]# useradd air
[root@localhost /]# passwd air
Changing password for user air.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.
```

2、删除用户（保留文件夹）
``` bash
[root@localhost /]# userdel sir
# 可以看到sir的文件夹并没有删除
[root@localhost /]# ls /home/
air  sir
```

3、删除用户（不保留文件夹）
``` bash
[root@localhost /]# userdel -r sir
```

4、编辑linux中所有组的信息
``` bash
[root@localhost /]# vi /etc/group
```

5、查看linux中所有组
``` bash
[root@localhost /]# cat /etc/group
```

6、给linux添加组（wzb是组的名字）
``` bash
[root@localhost /]#groupadd wzb
```

7、创建用户，并同时指定将该用户分配到哪个组(创建air用户分到wzb组)
``` bash
[root@localhost /]#useradd -g wzb air
```

8、将某个用户转移到其他组【只能管理员使用】（将air1移到wzb组）
``` bash
[root@localhost /]#usermod -g wzb air1
```

9、编辑所有用户信息
``` bash
[root@localhost /]# vi /etc/passwd
```

10、查看所有用户信息
``` bash
[root@localhost /]# cat /etc/passwd
```

11、查看当前用户是谁
``` bash
[root@localhost /]# who am i
root     pts/0        2016-07-06 07:40 (10.0.2.2)
```

12、给用户添加sudo权限，但不允许关机
``` bash
[root@localhost ~]# visudo
接着输入[:set nu]vi编辑器就会显示行号
100 ##关机
101 Cmnd_Alias SHUTDOWN = /sbin/halt, /sbin/shutdown, 
/sbin/poweroff, /sbin/reboot, /sbin/init
102 
103 air        ALL=(ALL) ALL,!SHUTDOWN
104 
105 Defaults logfile=/var/log/sudo.log
:wq
```

## 时间修改
linux系统时钟有两个，一个是硬件时钟，即BIOS时间，就是我们进行CMOS设置时看到的时间，另一个是系统时钟，是linux系统Kernel时间。当Linux启动时，系统Kernel会去读取硬件时钟的设置，然后系统时钟就会独立于硬件运作。有时我们会发现系统时钟和硬件时钟不一致，因此需要执行时间同步，下面就分享一下时间设置及时钟同步的命令使用方法

1、修改系统硬件时钟
``` bash
[root@localhost ~]# hwclock --set --date="07/28/16 13:54"
# 或者使用
[root@localhost ~]# clock --set --date="07/28/16 13:54"
``` 

2、查看系统硬件时间
``` bash
[root@localhost ~]# hwclock
[root@localhost ~]# hwclock --show
# 或者
[root@localhost ~]# clock
[root@localhost ~]# clock --show
``` 

3、修改系统时间
``` bash
# 修改时间为：07月28日13时38分2016年
[root@localhost ~]# date 0728135816
Thu Jul 28 13:58:00 MDT 2016
# 查看系统时间
[root@localhost ~]# date
Thu Jul 28 13:58:08 MDT 2016
``` 

4、硬件时钟与系统时钟同步
``` bash
# hc代表硬件时间，sys代表系统时间，即用硬件时钟同步系统时钟
[root@localhost ~]#hwclock --hctosys
# 或者
[root@localhost ~]#clock --hctosys
``` 

5、系统时钟和硬件时钟同步
``` bash
# hc代表硬件时间，sys代表系统时间，即用系统时钟同步硬件时钟
[root@localhost ~]#hwclock --systohc
# 或者
[root@localhost ~]#clock --systohc
``` 