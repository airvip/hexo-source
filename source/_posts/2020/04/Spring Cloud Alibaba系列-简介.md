---
title: Spring Cloud Alibaba系列-简介
date: 2020-04-03 11:10:22
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
---

> 彼岸花花开彼岸，断肠草草断肝肠。
 
2018 年 10 月 31 日，Spring Cloud Alibaba 正式入驻 Spring Cloud 官方孵化器并发布了第一个预览版本。Spring Cloud 本身其实只是一套微服务规范，并不是一个拿来即可用的框架，而 Spring Cloud Alibaba 的开源为开发者们提供了这套规范的实现方式。

<!-- more -->

## 最好的选择


> 2018 年 12 月 12 日，Spring 官方社区发生了一件震惊 Java 界的大事，Netflix 官方宣布了旗下大部分的微服务组件进入了Maintenance Mode（维护模式）。这也就意味着，Spring Cloud Netflix 最终会成为历史产物。Spring Cloud Alibaba 背靠阿里也注定将大放异彩。

[Spring Cloud Alibaba 中文文档](https://spring-cloud-alibaba-group.github.io/github-pages/hoxton/zh-cn/index.html)

Spring Cloud Alibaba 致力于提供微服务开发的一站式解决方案。此项目包含开发分布式应用服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。

依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里分布式应用解决方案，通过阿里中间件来迅速搭建分布式应用系统。

目前 Spring Cloud Alibaba 提供了如下功能:

1. 服务限流降级：支持 WebServlet、WebFlux, OpenFeign、RestTemplate、Dubbo 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
2. 服务注册与发现：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
3. 分布式配置管理：支持分布式系统中的外部化配置，配置更改时自动刷新。
4. Rpc服务：扩展 Spring Cloud 客户端 RestTemplate 和 OpenFeign，支持调用 Dubbo RPC 服务
5. 消息驱动能力：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
6. 分布式事务：使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题。
7. 阿里云对象存储：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
8. 分布式任务调度：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。
9. 阿里云短信服务：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。


组件



截至目前 Spring Cloud Alibaba 已经孵化成功

毕业版本依赖关系(推荐使用)

|  Spring Cloud Version	 | Spring Cloud Alibaba Version	 | Spring Boot Version |
| ---- | ---- | ---- |
| Spring Cloud Hoxton | 2.2.0.RELEASE | 2.2.X.RELEASE |
| Spring Cloud Greenwich| 2.1.1.RELEASE| 2.1.X.RELEASE |
| Spring Cloud Finchley| 2.0.1.RELEASE| 2.0.X.RELEASE |
| Spring Cloud Edgware| 1.5.1.RELEASE| 1.5.X.RELEASE | 


















