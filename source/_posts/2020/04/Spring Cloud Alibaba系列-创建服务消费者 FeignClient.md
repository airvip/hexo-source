---
title: Spring Cloud Alibaba系列-创建服务消费者 FeignClient
date: 2020-04-03 19:12:54
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
    - Nacos
---

> 朝闻道，夕死可矣。

 
Feign 是声明式的 web service 客户端，它让微服务之间的调用变得更简单了，类似 controller 调用 service。使用 Feign，只需要创建一个接口并加以注解。

<!-- more -->

Feign 具有可插拔的注解特性，可使用 Feign 注解和 JAX-RS 注解。
Feign 支持可插拔的编码器和解码器。
Feign 默认集成了 Ribbon，Nacos 对 Feign 做了很好的兼容，默认实现了负载均衡的效果

以后的开发我们肯定是选用 Feigin 来做客户端。

## 创建服务消费者

创建 spring-cloud-alibaba-nacos-feign-consumer 文件夹

## POM

进入 spring-cloud-alibaba-nacos-feign-consumer 文件夹，在该文件夹下创建 pom.xml 文件，内容如下

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

    <groupId>wang.diff</groupId>
    <artifactId>spring-cloud-alibaba-nacos-feign-consumer</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-cloud-alibaba-nacos-feign-consumer</name>
    <description>Learn Spring Cloud Alibaba Start</description>
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
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>wang.diff.spring.cloud.alibaba.nacos.feign.consumer.NacosFeignConsumerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

新增了 feign 依赖 `org.springframework.cloud:spring-cloud-starter-openfeign`

## 创建配置文件

application.yml 内容如下

```
spring:
  application:
    name: nacos-feign-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848

server:
  port: 9092

management:
  endpoints:
    web:
      exposure:
        include: "*"
```


## Application


```
package wang.diff.spring.cloud.alibaba.nacos.feign.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class NacosFeignConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosFeignConsumerApplication.class, args);
    }
}
```

## 创建 Feign 接口

通过 `@FeignClient("服务名")` 注解来指定调用某个服务。

```
package wang.diff.spring.cloud.alibaba.nacos.feign.consumer.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(value = "nacos-provider")
public interface CallService {

    @GetMapping(value = "/echo/{message}")
    String callService(@PathVariable("message") String message);

}
```

## Controller

创建名为  NacosConsumerController 测试用的 Controller 类

```
package wang.diff.spring.cloud.alibaba.nacos.feign.consumer.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import wang.diff.spring.cloud.alibaba.nacos.feign.consumer.service.CallService;

@RestController
public class NacosConsumerController {
    @Autowired
    private CallService callService;

    @Value("${spring.application.name}")
    private String appName;
    
    @GetMapping(value = "/echo/app-name")
    public String echo() {
        return callService.callService(appName);
    }

}
```

启动 NacosConsumerApplication 示例，这个时候你就可以在 Nacos 的控制台上看到注册上来的服务信息了。

## 测试
启动后，访问 Consumer 提供出来的 http://127.0.0.1:9092/echo/app-name 接口。

访问结果: Hello Nacos Discovery nacos-feign-consumer

## Nacos Discovery 对外暴露的 Endpoint

访问 `http://127.0.0.1:9092/actuator/nacos-discovery` 如下

```
{
    "subscribe": [ ], 
    "NacosDiscoveryProperties": {
        "serverAddr": "127.0.0.1:8848", 
        "endpoint": "", 
        "namespace": "", 
        "watchDelay": 30000, 
        "logName": "", 
        "service": "nacos-feign-consumer", 
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
        "port": 9092, 
        "secure": false, 
        "accessKey": "", 
        "secretKey": "", 
        "heartBeatInterval": null, 
        "heartBeatTimeout": null, 
        "ipDeleteTimeout": null
    }
}
```

## 生产者负载均衡测试

现在已经启动了一个 `nacos-provider` 实例，通过 将修改 nacos-provider 项目中 application.yml 文件的端口号为 8082 ，在起一个 `nacos-provider` 实例

修改 nacos-provider 的  NacosProviderApplication 为如下内容，其实就是多打印了 port, 用于查看负载均衡效果， 

```
package wang.diff.spring.cloud.alibaba.nacos.provider;

import org.springframework.beans.factory.annotation.Value;
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

    @Value("${server.port}")
    private String port;

    @RestController
    public class EchoController {
        @GetMapping(value = "/echo/{string}")
        public String echo(@PathVariable String string) {
            return "Hello Nacos Discovery " + string + " port： " + port;
        }
    }
}
``

在浏览器上多次访问 http://127.0.0.1:9092/echo/app-name ，浏览器交替显示：

```
Hello Nacos Discovery nacos-feign-consumer
Hello Nacos Discovery nacos-feign-consumer port: 8082
```

## 开启多实例

在 idea 菜单栏的 `Run` > `Edit Configurations...` > 在左侧点击对应的的项目，在右面板的右上角勾选 `Allow parallel run` 点击 `Apply` 应用即可。

![Allow parallel run](https://s1.ax1x.com/2020/04/03/Gat8SJ.png)