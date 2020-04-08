---
title: Spring Cloud Alibaba系列-服务熔断降级 Sentinel
date: 2020-04-08 09:46:55
tags: 
    - Spring Cloud Alibaba
    - Java
    - 微服务
    - Sentinel
---

> 当老天都不肯放过你的时候,不管怎么努力也都是徒劳......你说呢?

## [What] Sentinel 是什么

随着微服务的流行，服务和服务之间的稳定性变得越来越重要。 Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

<!-- more -->

Sentinel 具有以下特征:

* 丰富的应用场景： Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、实时熔断下游不可用应用等。
* 完备的实时监控： Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
* 广泛的开源生态： Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
* 完善的 SPI 扩展点： Sentinel 提供简单易用、完善的 SPI 扩展点。您可以通过实现扩展点，快速的定制逻辑。例如定制规则管理、适配数据源等。


## [Why] 为什么使用 Sentinel

微服务架构，我们往往会根据业务不同，将项目拆解成一个个的服务，服务与服务之间必然存在相互调用 (对内 RPC，对外 REST)，为了保证服务高可用，单个服务通常会集群部署。如果单个服务出现问题，调用这个服务就会出现线程阻塞，当大量请求涌入时，Servlet 容器的线程资源会被消耗完毕，导致服务瘫痪。由于服务与服务之间是相互依赖的，故障便会传播，从而导致整个服务不可用，后果可想而知。

会有如上问题，可定需要解决，难道你想加班不成？

为了解决这一问题，于是大牛们就提出了熔断器模型，可以理解为家用电器的过载保护。

Sentinel 就是阿里巴巴开源的一款实现了熔断器模型的组件。

![sentinel_fallback](https://s1.ax1x.com/2020/04/08/GRsU3V.png)

## [How] 如何使用 Sentinel

我们以 spring-cloud-alibaba-nacos-feign-consumer 项目为例来整合 Sentinel。

**依赖引入**

如果要在您的项目中引入 Sentinel，使用 group ID 为 `com.alibaba.cloud` 和 artifact ID 为 `spring-cloud-starter-alibaba-sentinel` 的 starter。

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

**在配置文件中开启 Sentinel**

新增如下代码

```
feign:
  sentinel:
    enabled: true
```

**在 service 中增加指定调用哪个 fallback 类**

```
package wang.diff.spring.cloud.alibaba.nacos.feign.consumer.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import wang.diff.spring.cloud.alibaba.nacos.feign.consumer.service.fallback.CallServiceFallback;

@FeignClient(value = "nacos-provider", fallback = CallServiceFallback.class)
public interface CallService {

    @GetMapping(value = "/echo/{message}")
    String callService(@PathVariable("message") String message);

}
```

**新增熔断器类并实现对应的 Feign 接口**

```
package wang.diff.spring.cloud.alibaba.nacos.feign.consumer.service.fallback;

import org.springframework.stereotype.Component;
import wang.diff.spring.cloud.alibaba.nacos.feign.consumer.service.CallService;

@Component
public class CallServiceFallback implements CallService {
    @Override
    public String callService(String  message) {
        return "callService fallback";
    }
}

```

将 NacosConsumerController 控制器的自动注入

```
    @Autowired
    private CallService callService;
```

修改为了手动注入

```
    private CallService callService;

    public NacosConsumerController(@Qualifier("callServiceFallback") CallService callService) {
        this.callService = callService;
    }
```

关闭服务提供者，通过浏览器访问 `http://127.0.0.1:9092/echo/app-name` 测试，结果如下：

```
callService fallback
```

## Sentinel 控制台

Sentinel 控制台提供一个轻量级的控制台，它提供机器发现、单机资源实时监控、集群资源汇总，以及规则管理的功能。您只需要对应用进行简单的配置，就可以使用这些功能。

注意: 集群资源汇总仅支持 500 台以下的应用集群，有大概 1 - 2 秒的延时。

### 获取 Sentinel 控制台

1. 可以从 [release](https://github.com/alibaba/Sentinel/releases) 页面 下载最新版本的控制台 jar 包。
2. 源码打包

```
# 下载源码
git clone https://github.com/alibaba/Sentinel.git
# 编译打包
mvn clean package
```

我推荐使用方式 1，因为目前源码编译要打包 39 个 jar 包，还经常失败，我试了 5 次才全部打包完成 。

### 启动控制台

Sentinel 控制台是一个标准的 SpringBoot 应用，以 SpringBoot 的方式运行 jar 包即可。

```
# 如果源码打包
cd sentinel-dashboard\target
# 开启（下载 jar 直接开启）
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```

如若 8080 端口冲突，可使用 `-Dserver.port=新端口` 进行设置。

| 参数	| 作用 |
| ---- | ---- |
|Dcsp.sentinel.dashboard.server=localhost:8080 |	向 Sentinel 接入端指定控制台的地址 |
| -Dproject.name=sentinel-dashboard	| 向 Sentinel 指定应用名称，比如上面对应的应用名称就为 sentinel-dashboard |

![sentinel_dashboard_login](https://s1.ax1x.com/2020/04/08/G2LvPP.png)

从 Sentinel 1.6.0 开始，Sentinel 控制台支持简单的登录功能，默认用户名和密码都是 sentinel。用户可以通过如下参数进行配置：

* -Dsentinel.dashboard.auth.username=sentinel 用于指定控制台的登录用户名为 sentinel；
* -Dsentinel.dashboard.auth.password=123456 用于指定控制台的登录密码为 123456；如果省略这两个参数，默认用户和密码均为 sentinel；
* -Dserver.servlet.session.timeout=7200 用于指定 Spring Boot 服务端 session 的过期时间，如 7200 表示 7200 秒；60m 表示 60 分钟，默认为 30 分钟；

在应用配置中添加 Sentinel 控制台信息

```
spring:
  cloud:
    sentinel:
      transport:
        port: 8720
        dashboard: localhost:8080
```

这里的 `spring.cloud.sentinel.transport.port` 端口配置会在应用对应的机器上启动一个 Http Server，该 Server 会与 Sentinel 控制台做交互。比如 Sentinel 控制台添加了 1 个限流规则，会把规则数据 push 给这个 Http Server 接收，Http Server 再将规则注册到 Sentinel 中。


我们 spring-cloud-alibaba-nacos-feign-consumer 项目完整的 application。yml 如下所示

```
spring:
  application:
    name: nacos-feign-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
    sentinel:
      transport:
        port: 8720
        dashboard: localhost:8080

feign:
  sentinel:
    enabled: true

server:
  port: 9092

management:
  endpoints:
    web:
      exposure:
        include: "*"
```

注：由于 8719 端口已被 `sentinel-dashboard` 占用，故这里修改端口号为 8720。


### 测试 Sentinel 控制台信息

浏览器先多访问 `http://127.0.0.1:9092/echo/app-name`
浏览器再访问 `http://localhost:8080/#/dashboard/home`

可以发现控制台多了一个名叫 nacos-feign-consumer 的服务

![sentinel-dashboard-index](https://s1.ax1x.com/2020/04/08/GR5amq.png)


