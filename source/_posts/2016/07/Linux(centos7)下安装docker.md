---
title: Linux(centos7)下安装docker
date: 2016-07-26 17:38:13
tags:
    - Docker
    - Centos
---

> 即使你已经习惯了受伤害，也有人看了会心疼的。

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。这篇主要为docker学习者的开篇之路。

<!-- more -->

牛逼就不多吹了，直接开始工作【该教程是在阿里云的服务器上做的】

## 操作系统
``` bash
[root@iZ2851a3hysZ ~]# uname -a
Linux iZ2851a3hysZ 3.10.0-123.9.3.el7.x86_64 #1 SMP 
Thu Nov 6 15:06:03 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
[root@iZ2851a3hysZ ~]#
[root@iZ2851a3hysZ ~]# cat /etc/redhat-release  # 查看内核版本
CentOS Linux release 7.0.1406 (Core) 
```

## 安装docker

``` bash
# 查看之前是否安装过docker
[root@iZ2851a3hysZ ~]# yum list installed | grep docker
# yum源中有木有docker
[root@iZ2851a3hysZ ~]# yum list | grep docker
cockpit-docker.x86_64                   0.108-1.el7.centos             extras   
docker.x86_64                           1.10.3-44.el7.centos           extras 
# 安装docker(yum如果出错,看篇尾错误是否一致，来解决)
root@iZ2851a3hysZ ~]# yum -y install docker
```
![docker安装](/img/201607/dockerinstall/dockerinstall.png)

安装完docker,可以看到安装的版本为1.10.3，现在就

开启docker,并加入开机启动项
``` bash
# 开启docker
[root@iZ2851a3hysZ ~]# systemctl start docker.service
# 加入开机启动
[root@iZ2851a3hysZ ~]# systemctl enable docker.service
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
# 查看docker的状态
[root@iZ2851a3hysZ ~]# systemctl status docker.service
```

几条docker命令
``` bash
[root@iZ2851a3hysZ ~]# docker –hel        # docker帮助
[root@iZ2851a3hysZ ~]# docker info        # Docker概要信息
[root@iZ2851a3hysZ ~]# docker images      # 镜像查看
[root@iZ2851a3hysZ ~]# docker ps -a       # 容器查看，即进程查看
```

安装镜像，可以到[官方查看镜像](https://registry.hub.docker.com/search?q=library)。

镜像，基于当前系统、Docker，制作的文件集合，可以是操作系统、程序，如centos镜像、ubuntu镜像、mysql镜像、Nginx镜像

## 安装 Nginx
``` bash
# 下载安装nginx镜像
[root@iZ2851a3hysZ ~]# docker pull nginx
Using default tag: latest
Trying to pull repository docker.io/library/nginx ... 
latest: Pulling from docker.io/library/nginx
51f5c6a04d83: Pull complete 
a3ed95caeb02: Pull complete 
51d229e136d0: Pull complete 
bcd41daec8cc: Pull complete 
Digest: sha256:0fe6413f3e30fcc5920bc8fa769280975b10b1c26721de956e1428b9e2f29d04
Status: Downloaded newer image for docker.io/nginx:latest
# 启动nginx
[root@iZ2851a3hysZ ~]# docker run -d -p 8888:80 nginx
3bf084d58e842a7ca53808d5bc3ae867a63762239024311541527894d177293c
```
**说明**容器内的nginx的80端口，映射到当前服务器的8888端口，当前服务器的ip是115.28.91.226，浏览器输入http://115.28.91.226:8888/，就可以看到nginx已启动，做了域名解析可以直接这样http://diff.wang:8888/

![运行nginx](/img/201607/dockerinstall/nginxrun.png)

再启动多一个容器，docker run -d -p 8887:80 nginx，浏览器输入http://diff.wang:8087/，就可以看到另外一个nginx已启动
到这里就能体现出Docker部署应用和传统部署应用的区别了，传统部署的话，需要人工拷贝多一份nginx，再配置端口，而Docker部署的话，在已制作好的镜像基础上，一条命令就可以部署一个新的应用

## 安装 centos7
``` bash
# 下载镜像安装
docker pull centos:7
# 启动容器
docker run -i -t centos:7 /bin/bash
这样就可以进入到centos7镜像系统
```
**问题**yum 错误解决
``` bash
在执行 yum -y install docker 的时候，报了如下的错误信息
Transaction check error:
  file /usr/lib/systemd/system/blk-availability.service from install of device-mapper-7:1.02.107-5.el7_2.5.x86_64 conflicts with file from package lvm2-7:2.02.105-14.el7.x86_64
  file /usr/sbin/blkdeactivate from install of device-mapper-7:1.02.107-5.el7_2.5.x86_64 conflicts with file from package lvm2-7:2.02.105-14.el7.x86_64
  file /usr/share/man/man8/blkdeactivate.8.gz from install of device-mapper-7:1.02.107-5.el7_2.5.x86_64 conflicts with file from package lvm2-7:2.02.105-14.el7.x86_64
# 解决方法
[root@iZ2851a3hysZ ~]# yum -y install libdevmapper*
# 再次安装docker
[root@iZ2851a3hysZ ~]# yum -y install docker
```

