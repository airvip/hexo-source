---
title: SSH端口转发讲解及模拟测试
date: 2021-01-15 23:27:51
tags: 
	- Ssh
---

> 风一直在走，永不会停

SSH 会自动加密和解密所有 SSH 客户端与服务端之间的网络数据，SSH 也可以将其他 TCP 端口的网络数据通 SSH 链接来转发，并且自动提供了相应的加密及解密服务。这一过程也被叫做"隧道"（tunneling），这是因为 SSH 为其他 TCP 链接提供了一个安全的通道来进行传输而得名。

<!-- more -->

SSH 端口转发的两大功能

1. 加密 SSH Client 端到 SSH Server 端之间的通讯数据。
2. 突破防火墙的限制，完成一些之前无法建立的 TCP 连接。

Telnet，SMTP，LDAP, HTTP 等 TCP 应用都可以这样转发实现访问。


## ssh安全隧道：本地端口转发

`请求机器` 让 `中间转发机` 转发 `我本地请求机器` 的请求

![1_ssh](https://s3.ax1x.com/2021/01/16/srdhLj.png)

**命令**

```
# -L 表示本地端口转发
ssh -L [local_request_addr:]local_request_port:remote_target_addr:remote_target_port middle_host
```
1. local_request_addr: 本地请求机器的地址
2. local_request_port：本地请求机器的端口号
3. remote_target_addr：目标机器的地址
4. remote_target_port：目标机器的端口号
5. middle_host：中间转发机器

* "-f" 表示后台启用
* "-N" 表示不打开远程 shell，处于等待状态（不加 -N 则直接登录进去）
* "-g" 表示启用网关功能



**模拟测试**

前提：三台模拟机 

* IP：192.168.226.130 （Ubuntu130）
* IP：192.168.226.136 （Ubuntu136）
* IP：192.168.226.137 （Ubuntu137） 

实现：本地端口转发

原理：130 通过 ssh 协议连接到 136 机器上，再通过 136 机器做跳板，连接至 137 机器。



1) 在 `Ubuntu137` 机器上拒绝 `Ubuntu130` 连接 

```
root@ubuntu137:~# iptables -A INPUT -s 192.168.226.130 -j REJECT
root@ubuntu137:~# iptables -L INPUT --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     all  --  192.168.226.130        anywhere             reject-with icmp-port-unreachable
```
此时在 `Ubuntu137` 多了一个阻止 `Ubuntu130` INPUT 的链路规则

此时，从 本地机器 用 ssh 命令连接 `Ubuntu137`，是拒绝的：
```
root@ubuntu130:~# ssh root@192.168.226.137
ssh: connect to host 192.168.226.137 port 22: Connection refused
root@ubuntu130:~#
```

2) 开启端口转发（ssh,telnet... 隧道）

查看 `Ubuntu130` 上开启了哪些端口

![1_ssh_ss-ntl](https://s3.ax1x.com/2021/01/16/sra14f.png)

我们选用端口 7777，从上图中可以看出，7777 端口没有被占用，所以可以使用

在 `ubuntu130` 我们建立本地转发的隧道

```
root@ubuntu130:~# ssh -L 7777:192.168.226.137:22 -fN 192.168.226.136
root@192.168.226.136's password:
root@ubuntu130:~#
```
密码输入正确之后，就成功建立隧道，在后台运行。可以通过 `ss -nt`查看连接情况

![1_ssh_130_ss-nt](https://s3.ax1x.com/2021/01/16/sraRbR.png)
![1_ssh_136_ss-nt](https://s3.ax1x.com/2021/01/16/sralUP.png)

3）在 `Ubuntu130` 上输入以下命令，用 7777 端口连接自己就可以连接到 `Ubuntu137` 了

```
root@ubuntu130:~# ssh root@127.0.0.1 -p 7777
```
![1_res-ssh_130_ss-nt](https://s3.ax1x.com/2021/01/16/sraQEt.png)


再看下 `Ubuntu136` 和 `Ubuntu137` 的连接情况：

![1_res-ssh_136_ss-nt](https://s3.ax1x.com/2021/01/16/srdrdI.png)
![1_res-ssh_137_ss-nt](https://s3.ax1x.com/2021/01/16/sraKHI.png)

分析：当 130 连接自己的 7777 端口时，该请求会自动通过 ssh 协议封装发送给 136 ，然后在 136 解封装,发送到 137。


**应用**

```
root@ubuntu130:~# ssh -L 7778:192.168.226.137:23 -fN 192.168.226.136
root@ubuntu130:~# telnet 127.0.0.1 7778
root@ubuntu130:~# 
root@ubuntu130:~# ssh -L 7779:192.168.226.137:80 -g 192.168.226.136
root@ubuntu130:~# curl 127.0.0.1:7779
```

**清理**

停止隧道，直接把后台的隧道进程杀死就可以了

```
root@ubuntu130:~# ps -aux | grep ssh
root       1171  0.0  0.4  65508  4024 ?        Ss   Jan15   0:00 /usr/sbin/sshd -D
root      39345  0.0  0.6  92828  6724 ?        Ss   01:20   0:00 sshd: root@pts/0
root      39486  0.0  0.0  44920   684 ?        Ss   01:58   0:00 ssh -L 7777:192.168.226.137:22 -fN 192.168.226.136
root      39582  0.0  0.1  12944  1020 pts/0    S+   03:31   0:00 grep --color=auto ssh
root@ubuntu130:~#
root@ubuntu130:~# kill -9 39486
root@ubuntu130:~#
root@ubuntu130:~# ss -nt
State       Recv-Q Send-Q         Local Address:Port                        Peer Address:Port
ESTAB       0      36           192.168.226.130:22                         192.168.226.1:58224
root@ubuntu130:~#
```

删除 INPUT 链阻止　130 访问
```
root@ubuntu137:~# iptables -L INPUT --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     all  --  192.168.226.130      anywhere             reject-with icmp-port-unreachable
# 删除 INPUT 链阻止　130 访问
root@ubuntu137:~# iptables -D INPUT 1
```


## ssh安全隧道：远程端口转发

`中间转发机` 转发 `远程请求机器` 的请求

![2_ssh](https://s3.ax1x.com/2021/01/16/srawEq.png)

**命令**

```
# -R 表示远程端口转发
ssh -L [middle_host_addr:]middle_host_port:remote_target_addr:remote_target_port remote_request_host
```

1. middle_host_addr: 中间转发机器地址
2. middle_host_port：中间转发机器端口号
3. remote_target_addr：目标机器地址
4. remote_target_port：目标机器端口号
5. remote_request_host: 远程请求机器地址

**模拟测试**

前提：三台模拟机 

* IP：192.168.226.130 （Ubuntu130）
* IP：192.168.226.136 （Ubuntu136）
* IP：192.168.226.137 （Ubuntu137） 

实现：远程端口转发

原理：136 转发 130 的请求至 137 机器。
　　

1) 在 `Ubuntu137` 机器上拒绝 `Ubuntu130` 连接 

```
root@ubuntu137:~# iptables -A INPUT -s 192.168.226.130 -j REJECT
root@ubuntu137:~#
```

此时，从 `Ubuntu130` 用 ssh 命令连接 `Ubuntu137`，是拒绝的。
```
root@ubuntu130:~# ssh root@192.168.226.137
ssh: connect to host 192.168.226.137 port 22: Connection refused
root@ubuntu130:~#
```

2) 开启端口转发（ssh,telnet... 隧道）

由于 136 不允许 130 连接了，所以我们要通过 136 去连接 130，因此，我们的开启隧道命令要在 136 上运行：

查看 `Ubuntu136` 上开启了哪些端口

![2_ssh_ss-ntl](https://s3.ax1x.com/2021/01/16/sra0U0.png)

我们选用端口 7777，从上图中可以看出，7777 端口没有被占用，所以可以使用

在 `ubuntu136` 我们建立远程转发的隧道

```
root@ubuntu136:~# ssh -R 7777:192.168.226.137:22 -fN 192.168.226.130
root@192.168.226.130's password:
root@ubuntu136:~#
```

密码输入正确之后，就成功建立隧道，在后台运行。可以通过 `ss -nt`查看连接情况

![2_ssh_130_ss-nt](https://s3.ax1x.com/2021/01/16/sraabn.png)
![2_ssh_136_ss-nt](https://s3.ax1x.com/2021/01/16/srawEq.png)

3）在 `Ubuntu130` 上输入以下命令，用 7777 端口连接自己就可以连接到 `Ubuntu137` 了

```
root@ubuntu130:~# ssh root@127.0.0.1 -p 7777
```
![2_res-ssh_130_ss-nt](https://s3.ax1x.com/2021/01/16/sraG8S.png)


再看下 `Ubuntu136` 和 `Ubuntu137` 的连接情况：

![2_res-ssh_136_ss-nt](https://s3.ax1x.com/2021/01/16/sraYvQ.png)
![2_res-ssh_137_ss-nt](https://s3.ax1x.com/2021/01/16/sraNuj.png)

分析：当 130 连接自己的 7777 端口时，该请求会自动通过 ssh 协议封装发送给 136 ，然后在 136 解封装,发送到 137。

**清理**

停止隧道，直接把后台的隧道进程杀死就可以了

```
root@ubuntu136:~# ps -aux | grep ssh
root       1178  0.0  0.1  65508  5588 ?        Ss   13:13   0:00 /usr/sbin/sshd -D
root       3461  0.0  0.1  92828  6868 ?        Ss   13:14   0:00 sshd: root@pts/0
root       4214  0.0  0.0  44920  2804 ?        Ss   14:39   0:00 ssh -R 7777:192.168.226.137:22 -fN 192.168.226.130
root       4222  0.0  0.0  12940  1020 pts/0    S+   15:03   0:00 grep --color=auto ssh
root@ubuntu136:~# kill -9 4214
```

删除 INPUT 链阻止　130 访问
```
root@ubuntu137:~# iptables -L INPUT --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     all  --  192.168.226.130      anywhere             reject-with icmp-port-unreachable
# 删除 INPUT 链阻止　130 访问
root@ubuntu137:~# iptables -D INPUT 1
```

## ssh安全隧道：动态端口转发(SOCKS代理)

![3_ssh](https://s3.ax1x.com/2021/01/16/sraB5V.png)

**命令**

```
# -D 表示动态端口转发
ssh -D [local_request_addr:]local_request_port middle_host_addr
```

1. local_request_addr: 本地请求机器的地址
2. local_request_port：本地请求机器的端口号
3. middle_host_addr: 中间转发机器地址

**模拟测试**

前提：三台模拟机 

* IP：192.168.226.130 （Ubuntu130）
* IP：192.168.226.136 （Ubuntu136）
* IP：192.168.226.137 （Ubuntu137） 

实现：动态端口转发

原理：国内 130 通过 136 机器做跳板，访问 137，138... 机器。

1) 在 `Ubuntu137` 机器上拒绝 `Ubuntu130` 连接，搭建 nginx 服务 

```
root@ubuntu137:~# iptables -A INPUT -s 192.168.226.130 -j REJECT
root@ubuntu137:~# apt -y install nginx
root@ubuntu137:~# vim /var/www/html/index.nginx-debian.html
只保留  <h1>Welcome to nginx!</h1>  即可
root@ubuntu137:~# curl 127.0.0.1
<h1>Welcome to nginx!</h1> 
root@ubuntu137:~#
```

此时，从 `Ubuntu130` 用 curl 请求 `Ubuntu137`，是拒绝的。
```
root@ubuntu130:~# curl 192.168.226.137
curl: (7) Failed to connect to 192.168.226.137 port 80: Connection refused
```


2) 开启端口转发

查看 `Ubuntu130` 上开启了哪些端口

![3_ssh_ss-ntl](https://s3.ax1x.com/2021/01/16/sray2F.png)

我们选用端口 7777，从上图中可以看出，7777 端口没有被占用，所以可以使用

在 `ubuntu130` 我们建立动态转发的隧道

```
root@ubuntu130:~# ssh -D 7777 -fN 192.168.226.136
root@192.168.226.136's password:
root@ubuntu130:~#
```

密码输入正确之后，就成功建立隧道，在后台运行。可以通过 `ss -nt`查看连接情况

![3_ssh_130_ss-nt](https://s3.ax1x.com/2021/01/16/srarCT.png)
![3_ssh_136_ss-nt](https://s3.ax1x.com/2021/01/16/sras8U.png)

3）在 `Ubuntu130` 上 设置代理 136 访问 137


```
root@ubuntu130:~# curl --socks5 127.0.0.1:7777 http://192.168.226.137
<h1>Welcome to nginx!</h1>
```

至此，我们就可以在 130 上通过 socks 代理访问 137 的 web 服务啦。 

**清理**

停止隧道，直接把后台的隧道进程杀死就可以了

```
root@ubuntu130:~# ps -aux | grep ssh
root       1131  0.0  0.5  65508  5420 ?        Ss   17:45   0:00 /usr/sbin/sshd -D
root       2209  0.0  0.6  92828  6708 ?        Ss   21:26   0:00 sshd: root@pts/1
root       2353  0.0  0.0  44920   688 ?        Ss   22:20   0:00 ssh -D 7777 -fN 192.168.226.136
root       2412  0.0  0.1  12944  1084 pts/1    S+   23:04   0:00 grep --color=auto ssh
root@ubuntu130:~# kill -9 2353
```

删除 INPUT 链阻止　130 访问  &&  卸载 nginx 服务
```
root@ubuntu137:~# iptables -L INPUT --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     all  --  192.168.226.130      anywhere             reject-with icmp-port-unreachable
# 删除 INPUT 链阻止　130 访问
root@ubuntu137:~# iptables -D INPUT 1
root@ubuntu137:~# 
root@ubuntu137:~# systemctl stop nginx
root@ubuntu137:~# apt --purge autoremove nginx
```

## 总结

我们已经完成了本地端口转发，远程端口转发，动态端口转发的模拟测试。

总的思路：通过将 TCP 连接转发到 SSH 通道上以解决数据加密以及突破防火墙的种种限制。

对一些已知端口号的应用，例如 Telnet/LDAP/SMTP/SSH，可以使用`本地端口转发`或者`远程端口转发`来达到目的。

`动态端口转发`则可以实现 SOCKS 代理从而加密以及突破防火墙对 Web 浏览的限制。

如果能灵活应用这些技巧，对我们的日常生活 / 工作也是会有所帮助的。