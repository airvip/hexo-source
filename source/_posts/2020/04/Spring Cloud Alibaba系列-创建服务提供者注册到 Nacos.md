---
title: Spring Cloud Alibaba系列-创建服务提供者
date: 2020-04-03 18:12:41
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
    - Nacos
---

> 听闻远方有你，即便跋涉千里也要去拥抱你...

 
服务发现是微服务架构体系中最关键的组件之一。如果尝试着用手动的方式来给每一个客户端来配置所有服务提供者的服务列表是一件非常困难的事，而且也不利于服务的动态扩缩容。Nacos Discovery 可以帮助我们将服务自动注册到 Nacos 服务端并且能够动态感知和刷新某个服务实例的服务列表。

<!-- more -->


## 引入 Nacos Discovery 进行服务注册/发现

我们通过示例来感受一下如何将服务注册到 Nacos

如果要在项目中使用 Nacos 来实现服务注册/发现，使用 group ID 为 com.alibaba.cloud 和 artifact ID 为 spring-cloud-starter-alibaba-nacos-discovery 的 starter。

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

## 创建服务提供者

创建 spring-cloud-alibaba-nacos-provider 文件夹

## POM

进入 spring-cloud-alibaba-nacos-provider 文件夹，在该文件夹下创建 pom.xml 文件，内容如下

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--继承了 Spring Boot 的 Parent，表示我们是一个 Spring Boot 工程-->
    <parent>
        <groupId>wang.diff</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath/>
    </parent>

    <!-- groupId: groupId:项目或者组织的唯一标志，并且配置时生成的路径也是由此生成，
       如wang.diff.app生成的相对路径为：/wang/diff/app -->
    <groupId>wang.diff</groupId>
    <!-- artifactId: 项目的通用名称 -->
    <artifactId>spring-cloud-alibaba-nacos-provider</artifactId>
    <!-- version:项目的版本 -->
    <version>1.0.0-SNAPSHOT</version>
    <!-- packaging: 打包的机制，如pom, jar, maven-plugin, ejb, war, ear, rar, par   -->
    <packaging>jar</packaging>
    <name>spring-cloud-alibaba-nacos-provider</name>
    <!-- 项目的描述, Maven 产生的文档用 -->
    <description>Learn Spring Cloud Alibaba Start</description>
    <!-- 哪个网站可以找到这个项目,提示如果 Maven 资源列表没有，可以直接上该网站寻找, Maven 产生的文档用 -->
    <url>https://diff.wang</url>
    <inceptionYear>2020-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
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
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>wang.diff.spring.cloud.alibaba.nacos.provider.NacosProviderApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## 创建配置文件

常用配置文件有 application.yml 与 application.properties 两种，可以根据个人喜好创建配置文件，我比较喜欢 yml，因为结构比较清晰

application.yml 内容如下

```
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848

server:
  port: 8081

management:
  endpoints:
    web:
      exposure:
        include: "*"
```

也可以使用 application.properties (后面的项目将不再提供properties格式的配置文件)

```
spring.application.name=nacos-provider
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
server.port=8081
management.endpoints.web.exposure.include=*
```

**如果不想使用 Nacos 作为您的服务注册与发现，可以将 spring.cloud.nacos.discovery 设置为 false。**

## Application

通过 @EnableDiscoveryClient 注解表明是一个 Nacos 客户端，该注解是 Spring Cloud 提供的原生注解

```
package wang.diff.spring.cloud.alibaba.nacos.provider;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@EnableDiscoveryClient
public class NacosProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosProviderApplication.class, args);
    }

    @RestController
    public class EchoController {
        @GetMapping(value = "/echo/{string}")
        public String echo(@PathVariable String string) {
            return "Hello Nacos Discovery " + string;
        }
    }
}
```

启动 NacosProviderApplication 示例，这个时候你就可以在 Nacos 的控制台上看到注册上来的服务信息了。

通过浏览器访问 http://localhost:8848/nacos，即 Nacos Server 网址

![nacos servers list](https://s1.ax1x.com/2020/04/03/GUJ5dO.png)


## Nacos Discovery 对外暴露的 Endpoint

Nacos Discovery 内部提供了一个 Endpoint, 对应的 endpoint id 为 nacos-discovery。
EndPoint 的访问地址为 `http://ip:port/actuator/nacos-discovery` 如 `http://127.0.0.1:8081/actuator/nacos-discovery`

Endpoint 暴露的 json 中包含了两种属性:

1. subscribe: 显示了当前服务有哪些服务订阅者
2. NacosDiscoveryProperties: 当前应用 Nacos 的基础配置信息

这是 Endpoint 暴露的 json 示例(因为我们还没有做该服务的消费者，所以 subscribe 为空):

```
{
    "subscribe": [ ], 
    "NacosDiscoveryProperties": {
        "serverAddr": "127.0.0.1:8848", 
        "endpoint": "", 
        "namespace": "", 
        "watchDelay": 30000, 
        "logName": "", 
        "service": "nacos-provider", 
        "weight": 1, 
        "clusterName": "DEFAULT", 
        "group": "DEFAULT_GROUP", 
        "namingLoadCacheAtStart": "false", 
        "metadata": {
            "preserved.register.source": "SPRING_CLOUD"
        }, 
        "registerEnabled": true, 
        "ip": "192.168.1.76", 
        "networkInterface": "", 
        "port": 8081, 
        "secure": false, 
        "accessKey": "", 
        "secretKey": "", 
        "heartBeatInterval": null, 
        "heartBeatTimeout": null, 
        "ipDeleteTimeout": null
    }
}
```

## 关于 Nacos Discovery Starter 更多的配置项信息

更多关于 Nacos Discovery Starter 的配置项如下所示:

|  配置项 | Key  | 默认值  | 说明  |
|---|---|---|---|
| 服务端地址  | spring.cloud.nacos.discovery.server-addr  |   |  Nacos Server 启动监听的ip地址和端口 |
| 服务名  | spring.cloud.nacos.discovery.service  |  ${spring.application.name} | 注册的服务名  |
| 权重  |  spring.cloud.nacos.discovery.weight | 1  | 取值范围 1 到 100，数值越大，权重越大  |
| 网卡名  | spring.cloud.nacos.discovery.network-interface  |   | 当IP未配置时，注册的IP为此网卡所对应的IP地址，如果此项也未配置，则默认取第一块网卡的地址  |
| 注册的IP地址  |  spring.cloud.nacos.discovery.ip |   |  优先级最高 |
| 注册的端口  | spring.cloud.nacos.discovery.port  |  -1 | 默认情况下不用配置，会自动探测  |
| 命名空间  | spring.cloud.nacos.discovery.namespace  |   | 常用场景之一是不同环境的注册的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等  |
| AccessKey  | spring.cloud.nacos.discovery.access-key  |   | 当要上阿里云时，阿里云上面的一个云账号名  |
| SecretKey  | spring.cloud.nacos.discovery.secret-key  |   | 当要上阿里云时，阿里云上面的一个云账号密码  |
| Metadata  | spring.cloud.nacos.discovery.metadata  |   |  使用Map格式配置，用户可以根据自己的需要自定义一些和服务相关的元数据信息 |
| 日志文件名  | spring.cloud.nacos.discovery.log-name  |   |   |
| 集群  | spring.cloud.nacos.discovery.cluster-name  |  DEFAULT | Nacos集群名称  |
| 接入点  |  spring.cloud.nacos.discovery.endpoint |   |  地域的某个服务的入口域名，通过此域名可以动态地拿到服务端地址 |
| 是否集成Ribbon |  ribbon.nacos.enabled |  true |  一般都设置成true即可 |
| 是否开启Nacos Watch | spring.cloud.nacos.discovery.watch.enabled | true | 可以设置成false来关闭 watch |
