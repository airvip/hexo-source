---
title: ssh 免密登录如何生成公钥私钥
date: 2017-02-05 23:46:27
tags:
    - SSH
---

> 追逐梦想的人比抓住梦想的人更能发挥实力。

SSH登录是需要用户名和密码的，要实现无密码登录，就需要创建SSH 密钥（ssh key），SSH 密钥可以认为是和另一台电脑通信时的唯一的识别证。

<!-- more -->

# 操作须知

关于 windows 系统需要先安装 git ,下面的命令也是在 git bash 中使用的，如果在安装 git 的时候，已经把 git bash 添加右键菜单，你可以直接在任意目录空白处右击直接调出 git bash,如果没有添加到右键菜单请自行在开始菜单或者安装目录中启动。

关于 Linux 或者 Mac 电脑，可以直接使用系统自带的 terminal 命令行终端。

# 生成秘钥

先检查是否已经存在 ssh key
``` bash
$ ls ~/.ssh
id_rsa  id_rsa.pub  known_hosts
```
如果存在 .ssh 文件夹，并且里面有内容，将会列出该文件夹下的所有文件。

| 文件名 | 说明 |
| :----- | :----- |
| id_rsa | 私钥文件 |
| id_rsa.pub | 公钥文件 |
| known_hosts | 已知远程主机文件 |

如果没有公钥和私钥对，或者不希望使用任何可用的密钥对来连接服务器，我们输入一下命令
```bash
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
将会创建一个新的 ssh 密钥，使用提供的电子邮件作为标签。
一般之后三个 enter 键即可
![ssh-keygen](/img/201702/sshkeygen/ssh-keygen.png)
现在在使用 `ls ~/.ssh` 至少看到了 id_rsa 与 id_rsa.pub 这两个文件。

# 参数说明
-t rsa 参数生成 rsa 格式的 私钥 和 公钥
-t dsa 参数生成 dsa 格式的 私钥 和 公钥
-t 后面还可以使用 ecdsa 或者 ed25519 或者 rsa1
-C 参数添加了密钥的注释，如果不需要这个参数也可以省略
对于 `ssh-keygen` 的详细使用可以使用 `ssh-keygen --help` 命令查看







