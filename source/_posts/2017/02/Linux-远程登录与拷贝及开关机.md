---
title: Linux 之远程登录、拷贝及开关机
date: 2017-02-02 23:09:15
tags:
    - Linux
---

> 十年过去了，时间没长大，我却变了。

现在 Linux 作为服务器市场上的绝对霸主，如果不会使用一些基本的管理命令，是不是就会显得非常的门外汉？这里记录一些基本命令以作备忘。

<!-- more -->

# ssh 远程登录
``` bash
$ ssh [-p port] user@remote
```
port 是远程机器 SSH Server监听的端口，如果不指定，就是默认值 22。
user 是远程机器上的用户名，如果不指定，默认是当前用户。
remote 是远程机器的地址，可以是 IP/域名。

* 说明
ssh 这个终端命令可以在 Linux 或者 Unix 系统系直接使用
退出当前用户的远程登录可以使用 `exit` 命令
一般在生产环境中为了安全会修改 SSH Server监听的端口

# shutdown 关机与重启

`shutdown` 命令可以安全关闭或者重新启动系统

* 说明
shutdown 不指定选项和参数，默认表示 1分钟之后关闭电脑
对于远程维护的服务器，最好不要关闭系统，而是将系统进行重启

常用命令
``` bash
#重新启动操作系统，参数 now 表示现在立刻
$ shutdown -r now

#理科关闭系统，参数 now 表示现在立刻
$ shutdown now

#设置系统在今天 22:00 关机
$ shutdown 22:00

#设置系统 5 分钟之后自动关机
$ shutdown +5

#取消之前的关机计划
$ shutdown -c
```

# scp 安全拷贝

scp 就是 secure copy,是一个在 Linux 下用来进行远程拷贝文件的命令
scp 对远程地址的格式与 ssh 基本相同，scp 在制定端口号的时候使用大写字母 `-P`

scp 常用参数说明

| 选项 | 描述 |
|:-----|:-----|
| -P | 若远程 ssh 服务器端口不是 22，需要使用大写字母 -P 指定端口 |
| -r | 如果源文件是个目录，scp 将递归复制该目录下的所有子目录，目标文件必须是一个目录名 |

常用命令
``` bash
#文件拷贝：将当前目录下的`02.txt`拷贝到远程主机 `/home/airvip/` 目录下
$ scp -P 22 ./02.txt airvip@112.112.112.252:/home/airvip/

#文件拷贝：将远程主机 `/home/airvip/01.txt`文件拷贝到当前目录下的
$ scp -P 22 airvip@112.112.112.252:/home/airvip/01.txt ./

#目录拷贝：将当前目录下的`test目录`拷贝到远程主机 `/home/airvip/` 目录下
$ scp -P 22 -r ./test airvip@112.112.112.252:/home/airvip/

#目录拷贝：将远程主机 `/home/airvip/demo2`目录拷贝到当前目录下的
$ scp -P 22 -r airvip@112.112.112.252:/home/airvip/demo2 ./
```

