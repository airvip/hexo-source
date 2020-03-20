---
title: docker-compose安装Jenkins
date: 2020-03-19 11:14:23
tags: 
    - Docker
    - Jenkins
---

> 只要世间有酒,我就喝不够!

Jenkins 是一个开源软件项目，是基于 Java 开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。

[官方网站](https://jenkins.io/)

<!-- more -->

当前 docker 版本：18.09.5
当前 docker-compose 版本
```
docker-compose version 1.25.1, build a82fef07
docker-py version: 4.1.0
CPython version: 3.7.4
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
```

## docker-compose.yml

创建文件 `/usr/local/docker/jenkins/docker-compose.yml` 进入 `/usr/local/docker/jenkins/` 目录，打开 `docker-compose.yml` 复制下面代码并粘贴

```
version: '3.1'
services:
  jenkins:
    restart: always
    image: 'jenkinsci/jenkins:2.150.1'
    container_name: 'jenkins'
    ports:
      - 8080:8080
      - 50000:50000
    environment:
      TZ: Asia/Shanghai
    volumes:
      - ./data:/var/jenkins_home
```

## 配置，构建服务

创建 data 目录并设置权限

```
[root@localhost jenkins]# mkdir data
[root@localhost jenkins]# chown -R 1000 /usr/local/docker/jenkins/data
```

构建 Jenkins 服务

```
[root@localhost jenkins]# docker-compose up -d
```

查看启动日志

```
[root@localhost jenkins]# docker logs -f jenkins

出现了一个如下的警告信息
WARNING: Upgrading Jenkins. Failed to update the default Update Site 'default'. Plugin upgrades may fail.

# 处理如下
[root@localhost jenkins]# docker stop jenkins
[root@localhost jenkins]# cd data
[root@localhost data]# vim hudson.model.UpdateCenter.xml

将 url 中的 https://updates.jenkins.io/update-center.json 更改为 http://updates.jenkins.io/update-center.json, 也就是去掉了 s
当然还有可以使用清华的镜像   https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

[root@localhost data]# cd ..
[root@localhost jenkins]# docker start jenkins
```

## 安装配置

访问 `http://ip:8080` 进行初始化

Jenkins 第一次启动时需要输入一个初始密码用以解锁安装流程，使用 docker logs jenkins 即可查看到初始密码，或者通过 `[root@localhost jenkins]# cat ./data/secrets/initialAdminPassword` 查看

![jenkins init password](https://s1.ax1x.com/2020/03/20/86Tlge.png)

**安装如果因为网速等原因导致安装时间比较长，只能等待等待。如果长时间停留在安装页，可以刷新试一下。** 

自定义插件的安装

![jenkins custom plugins](https://s1.ax1x.com/2020/03/20/86Tvxe.png)

我们可以在 `https://plugins.jenkins.io/` 查看所有的插件

**除了默认勾选的插件外，一定要勾选 Publish over SSH 插件，持续交付所必须的插件。**

![jenkins plugin ssh](https://s1.ax1x.com/2020/03/20/8675Jf.png)

点击安装 install 之后，又是漫长等待，没办法我的网速有点累人

![jenkins install plugins](https://s1.ax1x.com/2020/03/20/86Hyt0.png)

经过漫长等待，出现如下结果，不要慌，记住这些插件，之后还是可以继续安装的

![jenkins plugins](https://s1.ax1x.com/2020/03/20/86HoA1.png)

设置管理

![jenkins admin](https://s1.ax1x.com/2020/03/20/86bh28.png)

点击 `Save and Finish` ，完成安装设置，进入首页

![jenkins index](https://s1.ax1x.com/2020/03/20/86bzMF.png)









