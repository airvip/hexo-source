---
title: Spring Cloud Alibaba系列-创建服务消费者 LoadBalanceClient
date: 2020-04-03 18:59:41
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
    - Nacos
---

> 万书皆下品，唯有萌最高！

 
我们已经有了服务生产者，接下开肯定要创建个服务消费者感受以下

<!-- more -->

服务消费者与服务提供者相似，因为在 Consumer 端需要去调用 Provider 端提供的 REST 服务。我们使用最原始的一种方式， 即显示的使用 LoadBalanceClient 和 RestTemplate 结合的方式来访问。

> 通过带有负载均衡的 FeignClient 和 RestTemplate 也是可以访问的。（下一章）

## 创建服务消费者

创建 spring-cloud-alibaba-nacos-consumer 文件夹

## POM

进入 spring-cloud-alibaba-nacos-consumer 文件夹，在该文件夹下创建 pom.xml 文件，内容如下

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
    <artifactId>spring-cloud-alibaba-nacos-consumer</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-cloud-alibaba-nacos-consumer</name>
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
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>wang.diff.spring.cloud.alibaba.nacos.consumer.NacosConsumerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## 创建配置文件

application.yml 内容如下

```
spring:
  application:
    name: nacos-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848

server:
  port: 9091

management:
  endpoints:
    web:
      exposure:
        include: "*"
```


## Application


```
package wang.diff.spring.cloud.alibaba.nacos.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class NacosConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosConsumerApplication.class, args);
    }
}
```

## Configuration

创建名为 NacosConsumerConfiguration 的 Java 配置类，主要作用是为了注入 RestTemplate，所谓的无配置，并不是没有配置，而是减少了大量繁琐的 xml

使用 `@Configuration` 注解来标识这是 Java 配置类，
`@Configuration` 相当于原始的 spring-context.xml
`@Bean` 相当于原始的 `<bean id="restTemplate" class=xxxx>`

```
package wang.diff.spring.cloud.alibaba.nacos.consumer.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class NacosConsumerConfiguration {

    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

## Controller

创建名为  NacosConsumerController 测试用的 Controller 类

```
package wang.diff.spring.cloud.alibaba.nacos.consumer.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.loadbalancer.LoadBalancerClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class NacosConsumerController {

    @Autowired
    private LoadBalancerClient loadBalancerClient;
    @Autowired
    private RestTemplate restTemplate;

    @Value("${spring.application.name}")
    private String appName;

    @GetMapping("/echo/app-name")
    public String echoAppName(){
        //使用 LoadBalanceClient 和 RestTemplate 结合的方式来访问
        ServiceInstance serviceInstance = loadBalancerClient.choose("nacos-provider");
        String url = String.format("http://%s:%s/echo/%s",serviceInstance.getHost(),serviceInstance.getPort(),appName);
        System.out.println("request url:"+url);
        return restTemplate.getForObject(url,String.class);
    }
}
```

启动 NacosConsumerApplication 示例，这个时候你就可以在 Nacos 的控制台上看到注册上来的服务信息了。

## 测试
启动后，访问 Consumer 提供出来的 http://127.0.0.1:9091/echo/app-name 接口。

访问结果: Hello Nacos Discovery nacos-consumer

## Nacos Discovery 对外暴露的 Endpoint

访问 `http://127.0.0.1:9091/actuator/nacos-discovery` 如下

```
{
    "subscribe": [ ], 
    "NacosDiscoveryProperties": {
        "serverAddr": "127.0.0.1:8848", 
        "endpoint": "", 
        "namespace": "", 
        "watchDelay": 30000, 
        "logName": "", 
        "service": "nacos-consumer", 
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
        "port": 9091, 
        "secure": false, 
        "accessKey": "", 
        "secretKey": "", 
        "heartBeatInterval": null, 
        "heartBeatTimeout": null, 
        "ipDeleteTimeout": null
    }
}
```