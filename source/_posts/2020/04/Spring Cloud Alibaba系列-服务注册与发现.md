---
title: Spring Cloud Alibaba系列-服务注册与发现
date: 2020-04-03 16:30:00
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
    - Nacos
---

> 比自己,比梦想更重要的东西永远都存在着...

 
Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

<!-- more -->

所有的知识点最好以 [Nacos 官方文档](https://nacos.io/zh-cn/docs/what-is-nacos.html) 为基。

## 什么是 Nacos

服务（Service）是 Nacos 世界的一等公民。Nacos 支持几乎所有主流类型的“服务”的发现、配置和管理：

[Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/)

[gRPC](https://grpc.io/docs/guides/concepts.html#service-definition) & [Dubbo RPC Service](https://dubbo.incubator.apache.org/)

[Spring Cloud RESTful Service](https://spring.io/understanding/REST)

Nacos 的关键特性包括:

* 服务发现和服务健康监测
* 动态配置服务
* 动态 DNS 服务
* 服务及其元数据管理
* [更多特性](https://nacos.io/zh-cn/docs/roadmap.html)

## 快速开始安装 Nacos

Nacos 依赖 [Java](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 环境来运行。如果您是从代码开始构建并运行 Nacos，还需要为此配置 [Maven](https://maven.apache.org/index.html) 环境，请确保是在以下版本环境中安装使用:

1. 64 bit OS，支持 Linux/Unix/Mac/Windows，推荐选用 Linux/Unix/Mac。
2. 64 bit JDK 1.8+；[下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) & [配置](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)。
3. Maven 3.2.x+；[下载](https://maven.apache.org/download.cgi) & [配置](https://maven.apache.org/settings.html)。


### 下载源码或者安装包

我们可以通过源码和发行包两种方式来获取 Nacos。

从 Github 上下载源码方式

```
[root@localhost ~]# git clone https://github.com/alibaba/nacos.git
[root@localhost ~]# cd nacos/
[root@localhost ~]# mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U  
[root@localhost ~]# ls -al distribution/target/
// 进入编译目录
[root@localhost ~]# cd distribution/target/nacos-server-1.21/nacos/bin
``

下载编译后压缩包方式

从 [最新稳定版本](https://github.com/alibaba/nacos/releases) 下载。

```
unzip nacos-server-1.21.zip 或者 tar -xvf nacos-server-1.21.tar.gz
cd nacos/bin
```

### 启动服务

在 bin 目录下启动服务

```
# Linux 启动命令(standalone代表着单机模式运行，非集群模式)
./startup.sh -m standalone

# Windows
startup.cmd
```

![Nacos started](https://s1.ax1x.com/2020/04/03/GUC4VU.png)

### 访问服务

打开浏览器访问: http://127.0.0.1:8848/nacos/index.html, 默认账号 nacos 密码 nacos


![nacos_index](https://s1.ax1x.com/2020/04/03/GUiSmV.png)


