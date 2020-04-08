---
title: Spring Cloud Alibaba系列-网关路由统一的访问接口
date: 2020-04-08 12:11:01
tags: 
    - Spring Cloud Gateway
    - Java
    - 微服务
---

> 计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决。   — David Wheeler

在微服务架构中，服务的粒度被进一步细分，各个业务服务可以被独立的设计、开发、测试、部署和管理。如果都是采用客户端和服务器直连的话，维护起来工作量巨大，服务越多，越容易出错。所以需要网关（Gateway）。网关将分散在各个业务系统微服务的 API 聚合点和统一接入点，外部请求通过访问这个接入点，即可访问内部所有的 REST API 服务。


<!-- more -->

## [What] Spring Cloud Gateway 是什么

[Spring Cloud Gateway 官网指导手册](https://spring.io/projects/spring-cloud-gateway#overview)

这个项目提供了一个在 Spring MVC 之上构建 API 网关的库。Spring Cloud Gateway 旨在提供一种简单而有效的方法来路由到 APIS，并为它们提供跨领域的关注点，例如：安全性、监控/度量和弹性。


Spring Cloud Gateway 具有以下特征:

* 基于 Spring Framework 5，Project Reactor 和 Spring Boot 2.0
* 动态路由
* Predicates 和 Filters 作用于特定路由
* 集成 Hystrix 断路器
* 集成 Spring Cloud DiscoveryClient
* 易于编写的 Predicates 和 Filters
* 限流
* 路径重写


## [Why] 为什么使用 Spring Cloud Gateway

Spring Cloud Gateway 可以看做是一个 Zuul 1.x 的升级版和代替品，比 Zuul 2 更早的使用 Netty 实现异步 IO，从而实现了一个简单、比 Zuul 1.x 更高效的、与 Spring Cloud 紧密配合的 API 网关

Spring Cloud Gateway 里明确的区分了 Router 和 Filter，并且一个很大的特点是内置了非常多的开箱即用功能，并且都可以通过 SpringBoot 配置或者手工编码链式调用来使用。

Spring Cloud Gateway 和 Zuul2 的性能差不多，大概是直连的 40%；

Spring Cloud Gateway/Zuul2 对于 Java 技术栈来说比较方便，可以依赖业务系统的一些 common jar。

[对比测试源代码](https://github.com/kimmking/atlantis)

一般跟着 Spring 前进准没错，所以我们选择 Spring Cloud Gateway。

## [How] 如何使用 Spring Cloud Gateway

创建 spring-cloud-gateway 文件夹

### POM

进入 spring-cloud-gateway 文件夹，在该文件夹下创建 pom.xml 文件，内容如下

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>wang.diff</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath/>
    </parent>

    <groupId>wang.diff</groupId>
    <artifactId>spring-cloud-gateway</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-cloud-gateway</name>
    <description>Learn Spring Cloud Gateway Start</description>
    <url>https://diff.wang</url>
    <inceptionYear>2020-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

        <!-- Commons Begin -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
        </dependency>
        <!-- Commons Begin -->

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>wang.diff.spring.cloud.gateway.GatewayApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要增加了 `org.springframework.cloud:spring-cloud-starter-gateway` 依赖


### 创建配置文件

application.yml 内容如下

```
spring:
  application:
    # 应用名称
    name: spring-gateway
  cloud:
    # 使用 Nacos 作为服务注册发现
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
    # 使用 Sentinel 作为熔断器
    sentinel:
      transport:
        port: 8721
        dashboard: localhost:8080
    gateway:
      # 表明 gateway 开启服务注册和发现的功能，并且 spring cloud gateway 自动根据服务发现为每一个服务创建了一个 router，
      # 这个 router 将以服务名开头的请求路径转发到对应的服务。
      discovery:
        locator:
          enabled: true

server:
  port: 9000

management:
  endpoints:
    web:
      exposure:
        include: "*"

logging:
  level:
    org.springframework.cloud.gateway: trace
```

### Application

```
package wang.diff.spring.cloud.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### 测试

开启全部服务
* Nacos 服务
* Sentinel 服务
* NacosProviderApplication
* NacosConsumerApplication
* NacosFeignConsumerApplication
* GatewayApplication

打开浏览器访问：`http://localhost:9000/nacos-provider/echo/app-name` 浏览器显示如下

```
Hello Nacos Discovery app-name port: 8081
```

打开浏览器访问：`http://localhost:9000/nacos-consumer/echo/app-name` 浏览器显示如下

```
Hello Nacos Discovery nacos-consumer port: 8081
```

打开浏览器访问：`http://localhost:9000/nacos-feign-consumer/echo/app-name` 浏览器显示如下

```
Hello Nacos Discovery nacos-feign-consumer port: 8081
```

