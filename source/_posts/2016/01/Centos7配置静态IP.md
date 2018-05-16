---
title: Centos7配置静态IP
date: 2016-01-13 11:46:55
tags:
    - Linux
    - Centos
---

> 无论乌云有多浓厚，星星也一定还在，只是暂时看不到了而已。

在CentOS 7上，将其中一个网络接口从DHCP改为静态IP地址配置，永久为CentOS或RHEL 7上的网络接口分配静态IP地址。

<!-- more -->

centos7增强了对ipv6的支持，然而当下ipv4还是大权在握，安装好了系统，要想跑服务当然需要配置ip,现在我们就以vbox里面安装的centos7,继续耍。

## 操作图示
选定要配置的虚拟机->设置->网络->网络地址转换->端口转发->添加端口映射（自有他用）
![step.jpg](/img/201601/ipcentos/step.jpg)


## 配置步骤

1、查看当前的操作环境
```
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 7.1.1503 (Core) 
```

2、配置静态IP
传送门：[官方WIKI指导配置静态IP](https://wiki.centos.org/FAQ/CentOS7#head-a21a9e454157700367c9b7e9ccb1ff9954bec881)
具体配置方案，需要配置两个地方，所有操作需要管理员（root）权限！
查看ip
```
[root@localhost network-scripts]# ip add
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:aa:ff:8c brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/8 brd 10.255.255.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feaa:ff8c/64 scope link 
       valid_lft forever preferred_lft forever
```
路径/etc/sysconfig/network-scripts,文件ifcfg-enp03
```
[root@localhost network-scripts]# vi ifcfg-enp0s3 
TYPE=Ethernet
#BOOTPROTO=dhcp
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
NAME=enp0s3
UUID=d79316cb-9065-424b-9858-0a4ee3e03a4a
DEVICE=enp0s3
#ONBOOT=no

#static assignment
ONBOOT=yes #开机启用该配置
BOOTPROTO=static #静态
IPADDR=10.0.2.15 #本机ip
#NETMASK=255.255.255.0 子网掩码注释掉之后为255.0.0.0
GATEWAY=10.0.2.2 #默认网关
NM_CONTROLLED=no 
```
详解：NM_CONTROLLED=yes|no  项决定了你的eth0是否可以由NNetwork Manager托管。
当NM_CONTROLLED=yes 时，你想使你的网卡配置生效，要重启下NetworkManager服务后，再重启network服务就行了，这时你唯一的好处就是可以用NetworkManager来管理你的网卡设备了，比如eth0,ppp0等；
当NM_CONTROLLED=no时，你想使你的网卡配置生效，不用重启NetworkManager服务，直接重启network服务就行了，这时你唯一的坏处就是不能用NetworkManager来管理你的网卡设备了，比如eth0，ppp0等。

3、DNS 官方建议在 /etc/sysconfig/network 中配置，比较简单直接给出配置（这一步可以跳过,但是你将ping不通域名，所以还是老实工作）
```
# Created by anaconda
DNS1=10.0.2.3
DNS2=8.8.8.8
```

4、重置网络配置（重启是最好选择）
```
[root@localhost network-scripts]# service network restart
Restarting network (via systemctl):                        [  OK  ]
```

5、做个ping通测试
```
[root@localhost network-scripts]# ping 192.168.56.1
PING 192.168.56.1 (192.168.56.1) 56(84) bytes of data.
64 bytes from 192.168.56.1: icmp_seq=1 ttl=64 time=5.81 ms
64 bytes from 192.168.56.1: icmp_seq=2 ttl=64 time=1.30 ms
64 bytes from 192.168.56.1: icmp_seq=3 ttl=64 time=1.34 ms
64 bytes from 192.168.56.1: icmp_seq=4 ttl=64 time=1.24 ms
^C
--- 192.168.56.1 ping statistics ---
```
<span style="color:red">ctrl+c 停止ping测试，如果ping不同，重启虚拟机再测试(reboot)</span>

6、再次查看ip信息
```
[root@localhost network-scripts]#ip a
```
