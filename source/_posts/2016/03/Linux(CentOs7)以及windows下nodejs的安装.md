---
title: Linux(CentOs7)以及windows下nodejs的安装
date: 2016-03-28 22:53:22
tags:
    - Node
---

> 忘记本身就是一件不可能的事。

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型,使其轻量又高效。

<!-- more -->

## windows下安装

[直接去官网下载msi软件包，下一步下一步安装就好](https://nodejs.org/en/download/)

**警告**：安装完了你不要删除这个msi软件包，在你卸载或者升级的时候自然会用到

## Linux(centos7)下安装

方式一：采用yum安装

1、查看安装源里面有木有nodejs
``` bash
[root@localhost ~]# yum list | grep nodejs
nodejs.x86_64                           0.10.42-4.el7                  epel     
nodejs-abbrev.noarch                    1.0.4-6.el7                    epel   
```

2、yum安装nodejs
``` bash
[root@localhost ~]# yum -y install nodejs
nodejs_installed.jpg
```
![yum insall -y nodejs](/img/201603/nodejs/node.jpg)
**备注**：安装的版本可能比较旧，分情况使用

方式二：采用编译安装

1、下载nodejs  （所需不同版本请修改url路径）
``` bash
[root@localhost ~]# wget http://nodejs.org/dist/v6.1.0/node-v6.1.0.tar.gz
```

2、解压nodejs
``` bash
[root@localhost ~]# tar -zxvf node-v6.1.0.tar.gz
```

3、重命名解压的文件夹
``` bash
[root@localhost ~]# mv node-v6.1.0 node
```

4、进入node文件夹
``` bash
[root@localhost ~]# cd node
```

5、安装gcc gcc-c  (不管之前有没有安装，还是运行一下，预防编译时报错)
``` bash
[root@localhost node]# sudo yum install gcc gcc-c++
```

6、运行配置
``` bash
[root@localhost node]# ./configure
```

7、编译安装   （不说了我的是在虚拟机中安装的，编译太慢了。。。）  
``` bash
[root@localhost node]# make install
```

8、查看一下版本
``` bash
[root@localhost ~]# node -v
v6.1.0
```
