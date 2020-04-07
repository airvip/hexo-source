---
title: Spring Cloud Alibaba系列-服务配置 Nacos Config
date: 2020-04-07 11:54:21
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
    - Nacos
---

> 彼岸花花开彼岸，断肠草草断肝肠。

Nacos 是一个 Alibaba 开源的、易于构建云原生应用的动态服务发现、**配置管理**和服务管理平台。

为了方便服务配置文件统一管理，实时更新，我们就需要配置中心组件。Nacos 自带了配置管理功能，使用 Spring Cloud Alibaba Nacos Config，可基于 Spring Cloud 的编程模型快速接入 Nacos 配置管理功能。

Spring Cloud Alibaba Nacos Config 是 Spring Cloud Config Server 和 Client 的替代方案。

<!-- more -->


## 引入 Nacos Config 进行配置管理

我们通过示例来感受一下如何使用 Nacos 进行配置管理，我们以 **服务提供者** (spring-cloud-alibaba-nacos-provider) 为例

如果要在您的项目中使用 Nacos 来实现配置管理，使用 group ID 为 com.alibaba.cloud 和 artifact ID 为 spring-cloud-starter-alibaba-nacos-config 的 starter。

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

## 快速开始

浏览器打开 http://127.0.0.1:8848/nacos ，访问 Nacos Server，

![nacos_config_index](https://s1.ax1x.com/2020/04/07/GghFjP.png)

新建配置

![nacos_config_add](https://s1.ax1x.com/2020/04/07/GghP1I.png)

**说明: Data ID 的默认扩展名为 .properties ，希望使用 YAML 配置，此处必须指明是 .yaml**

点击发布后，我们返回就可以看到我们刚刚新建的配置文件了。

![nacos_config_list](https://s1.ax1x.com/2020/04/07/Gghnhj.png)


## 客户端使用


对 **服务提供者** (spring-cloud-alibaba-nacos-provider) 的 pom.xml进行修改，修改后，完整 pom.xml 文件如下

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
    <artifactId>spring-cloud-alibaba-nacos-provider</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-cloud-alibaba-nacos-provider</name>
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
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
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

在 **服务提供者** (spring-cloud-alibaba-nacos-provider) 项目的 `src/main/resources` 目录下创建 `bootstrap.properties` 的配置文件并删除之前创建的 application.yml 配置文件,因为我们将使用 nacos 服务端做的配置。

```
# 这里的应用名对应 Nacos Config 中的 Data ID，习惯上将 Nacos Config 中 Data ID 配置为项目名
spring.application.name=nacos-provider-config
# 默认配置扩展名 properties，如果使用 yaml,就需要添加该配置
spring.cloud.nacos.config.file-extension=yaml
# Nacos Server 的地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
```

**说明: Spring Boot 配置文件的加载顺序，依次为 `bootstrap.properties` -> `bootstrap.yml` -> `application.properties` -> `application.yml` ，其中 `bootstrap.properties` 配置优先级最高**


![provider_config_from_nacos](https://s1.ax1x.com/2020/04/07/GghM3n.png)


## 配置的动态更新

从 Nacos Server 修改下我们的配置，添加

```
username: airvip
age: 24
```

![nacos_config_update](https://s1.ax1x.com/2020/04/07/GghQcq.png)

修改服务提供者应用动态读取配置

```
package wang.diff.spring.cloud.alibaba.nacos.provider;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.context.ConfigurableApplicationContext;
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

    @Autowired
    private ConfigurableApplicationContext applicationContext;

    @RestController
    public class EchoController {
        @GetMapping(value = "/echo/{string}")
        public String echo(@PathVariable String string) {

            System.out.println("name = " + applicationContext.getEnvironment().getProperty("username"));
            System.out.println("age = " + applicationContext.getEnvironment().getProperty("age"));

            return "Hello Nacos Discovery " + string + " port: " + port;
        }
    }
}

```

重启服务，浏览器访问 `http://localhost:8082/echo/test` 可以看到我们控制台已经打印了

```
name = airvip
age = 24
```

再修改下 Nacos Server 的 `name = airvip1` 配置，浏览器访问 `http://localhost:8082/echo/test`, 在控制台我们可以动态的打印我们的配置了.


**说明：你可以使用 `spring.cloud.nacos.config.refresh.enabled=false` 来关闭动态刷新**

## profile 粒度的配置（多环境配置）

Nacos Config 在加载配置的时候，不仅仅加载了以 DataId 为 `${spring.application.name}.${file-extension:properties}` 为前缀的基础配置，还加载了DataId为 `${spring.application.name}-${profile}.${file-extension:properties}` 的基础配置。在日常开发中如果遇到多套环境下的不同配置，可以通过Spring 提供的 `${spring.profiles.active}` 这个配置项来配置。

### 在 Nacos Server 中增加配置

增加一个名为 `nacos-provider-config-dev.yaml` 的配置

![nacos_config_add_dev](https://s1.ax1x.com/2020/04/07/GghEB8.png)

只是修改了端口号

### 在项目中增加配置

增加一个名为 `bootstrap-dev.properties` 的配置文件，内容如下：

```
spring.profiles.active=dev
spring.application.name=nacos-provider-config
spring.cloud.nacos.config.file-extension=yaml
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
```

主要增加了 `spring.profiles.active=dev` 配置，用于指定访问 Nacos Server 中的 `nacos-provider-config-dev.yaml` 配置

### 设置idea的激活配置

此时我们有两个配置文件，分别为 bootstrap.properties 和 bootstrap-dev.properties ，我们需要指定启动时加载哪一个配置文件，操作流程如下

IDEA 菜单栏 `Run` -> `Edit Configurations..`

![active_dev](https://s1.ax1x.com/2020/04/07/GgheAg.png)

再次启动，我们可以看到已经成功加载 dev 的配置了。

![active_dec_nacos](https://s1.ax1x.com/2020/04/07/GghmNQ.png)