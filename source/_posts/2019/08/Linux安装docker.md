---
title: Linux安装docker
date: 2019-08-28 01:54:20
tags:
    - Docker
---

> 累了就休息下，前面的路还很长远...

2017年的3月1号之后，Docker 的版本命名开始发生变化，同时将 CE 版本和 EE 版本进行分开。

Docker社区版（CE）：为了开发人员或小团队创建基于容器的应用,与团队成员分享和自动化的开发管道。docker-ce 提供了简单的安装和快速的安装，以便可以立即开始开发。docker-ce 集成和优化，基础设施。（免费） 
Docker企业版（EE）：专为企业的发展和IT团队建立谁。docker-ee 为企业提供最安全的容器平台，以应用为中心的平台。（付费）

所以我们现在安装肯定是安装 docker-ce 了。

<!-- more -->

玩 docker 是有系统要求的，我的系统环境环境如下

```
root@ubuntu:~# uname -a
Linux ubuntu 4.4.0-21-generic #37-Ubuntu SMP Mon Apr 18 18:33:37 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

root@ubuntu:~# lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04 LTS
Release:	16.04
Codename:	xenial
```

## 使用脚本自动安装

在测试或者开发环境中docker官方为了简化安装流程，提供了一套便捷的安装脚本，在 Ubuntu 上我们使用这套脚本进行安装


``` Bash
# 下载脚本
root@ubuntu:~# curl -fsSL get.docker.com -o get-docker.sh
# 使用 ali 镜像安装
root@ubuntu:~# sh get-docker.sh --mirror Aliyun
```

![安装完成](https://s2.ax1x.com/2020/01/09/lR1Yc9.png)

docker 查看版本

```
root@ubuntu:~# docker version
```

## 更换 docker 镜像仓库
对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请先创建该文件）

``` json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

*确保该文件复合json规范，否则docker无法启动*

更换完镜像仓库之后，重新启动服务

```
root@ubuntu:~# systemctl daemon-reload
root@ubuntu:~# systemctl restart docker
```

从镜像仓库拉取tomcat镜像

```
root@ubuntu:~# docker pull tomcat
```

从镜像仓库拉取指定版本tomcat镜像

```
root@ubuntu:~# docker pull tomcat:9-jdk8
```

查看本地拉取下来的镜像
```
root@ubuntu:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              ed94f55483b8        4 days ago          507MB
tomcat              9-jdk8              17acdd4eb11d        4 days ago          508MB
tomcat              9-jre8              e24825d32965        7 months ago        464MB
```

启动指定的 tomcat

```
root@ubuntu:~# docker run -p 8080:8080 e24825d32965
```

在浏览器测试浏览

![tomcat](https://s2.ax1x.com/2020/01/09/lRYwWT.png)

## 虚悬镜像

删除虚悬镜像
```
root@ubuntu:~# docker image prune
```


## 安装 docker-compose

Docker-Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。
Docker-Compose将所管理的容器分为三层，分别是工程（project），服务（service）以及容器（container）。

```
root@ubuntu:~# curl -L https://github.com/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
root@ubuntu:~# chmod +x /usr/local/bin/docker-compose
```
