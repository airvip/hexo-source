---
title: Spring Cloud Alibaba系列-链路追踪 SkyWalking
date: 2020-04-09 10:03:00
tags: 
    - SkyWalking
    - Java
    - 微服务
---

> 曾经沧海难为水，除却巫山不是云。

链路追踪，就是指一次请求任务的开始到结束，期间调用的所有系统 (服务) 及耗时（时间跨度）都可以完整记录下来。通过链路追踪我们可以非常方便的分析各个系统 (服务) 的性能，，以便发生故障的时候，能够快速定位和解决问题，这就是所谓的 APM（应用性能管理）。

<!-- more -->

## [What] Apache SkyWalking 是什么

目前主要的一些 APM 工具有: Cat、Zipkin、Pinpoint、SkyWalking。

Apache SkyWalking 已成为 Apache 基金会顶级项目。
Apache SkyWalking 完全由国人主导开发，在国内社区相当活跃。
Apache SkyWalking 是分布式系统的应用程序性能监视工具，专为微服务、云原生架构和基于容器（Docker、K8s、Mesos）架构而设计。
Apache SkyWalking 是观察性分析平台和应用性能管理系统。提供分布式追踪、服务网格遥测分析、度量聚合和可视化一体化解决方案。支持Java, .Net Core, PHP, NodeJS, Golang, LUA语言探针，支持Envoy + Istio构建的Service Mesh。

![SkyWalking_struct]()


* Skywalking Agent： 使用 JavaAgent 做字节码植入，无侵入式的收集，并通过 HTTP 或者 gRPC 方式发送数据到 SkyWalking Collector。
* SkyWalking Collector： 链路数据收集器，对 agent 传过来的数据进行整合分析处理并落入相关的数据存储中。
* Storage： SkyWalking 的存储，时间更迭，2020 年 3 月 22 日 SkyWalking APM 7.0.0 发布, 探针升级到 JDK1.8，支持代码级性能剖析
* UI： Web 可视化平台，用来展示落地的数据。

### Apache SkyWalking 功能特性

* 多种监控手段，语言探针和服务网格(Service Mesh)
* 多语言自动探针，Java，.NET Core 和 Node.JS
* 轻量高效，不需要大数据
* 模块化，UI、存储、集群管理多种机制可选
* 支持告警
* 优秀的可视化方案


## [Why] 为什么使用 Apache SkyWalking

微服务极大地改变了软件的开发和交付模式，单体应用被拆分为多个微服务，单个服务的复杂度大幅降低，库之间的依赖也转变为服务之间的依赖。由此带来的问题是部署的粒度变得越来越细，众多服务给运维带来巨大压力。客户端使用 REST 调用一个接口，可能需要多个服务协同才能完成这个功能，如果链路上任何一个服务出现问题或者网络超时，都会形成导致接口调用失败。随着业务的不断扩张，服务之间互相调用会越来越复杂。

因此，我们就需要些可以帮助理解系统行为、用于分析性能问题的工具。基于 Apache SkyWalking 种种特性 (最主要是国人开发)，所以他成了我们的不二之选。


## [How] 如何使用 Apache SkyWalking


Apache SkyWalking 分为服务端与客户端两部分。

### 服务端配置

SkyWalking 存储方案有多种，官方推荐的方案是 ElasticSearch ，并且最新的 SkyWalking APM 7 也已经支持 ElasticSearch 7，所以我们需要安装最版本 ElasticSearch。

docker-compose.yml

```

```
