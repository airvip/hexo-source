---
title: Spring Cloud Alibaba系列-网关路由全局过滤
date: 2020-04-08 18:30:11
tags: 
    - Spring Cloud Gateway
    - Java
    - 微服务
---

> 计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决。   — David Wheeler

网关经常需要对路由请求进行过滤，进行一些操作，如鉴权之后构造头部之类的，过滤的种类很多，如：权限认证、增加请求头、增加请求参数和增加响应头等等，这就用到了Spring Cloud Gateway 的 Filter。

<!-- more -->

**生命周期**

Spring Cloud Gateway 的 Filter 的生命周期不像 Zuul 的那么丰富，它只有两个：PRE 和 POST。

PRE： 过滤器在请求被路由之前调用。可用来实现身份验证、在集群中选择请求的微服务、记录调试信息等。
POST：过滤器在路由到微服务以后执行。可用来为响应添加标准的 HTTP Header、收集统计信息和指标、将响应从微服务发送给客户端等。

![filter](https://s1.ax1x.com/2020/04/08/GfQ2X6.png)


**分类**

Spring Cloud Gateway 的 Filter 从作用范围可分为另外两种 GatewayFilter 与 GlobalFilter。

* GatewayFilter：应用到单个路由或者一个分组的路由上。
* GlobalFilter：应用到所有的路由上。


## [What] Spring Cloud Gateway 之 Global filter 是什么

Spring Cloud Gateway 框架内置的 GlobalFilter 如下：

* Forward Routing Filter
* The LoadBalancerClient Filter
* The ReactiveLoadBalancerClientFilter
* The Netty Routing Filter
* The Netty Write Response Filter
* The RouteToRequestUrl Filter
* The Websocket Routing Filter
* The Gateway Metrics Filter

## [Why] 为什么使用 Spring Cloud Gateway

全局过滤器作用于所有的路由，不需要单独配置，可以用它来实现很多统一化处理的业务需求，比如权限认证，IP 访问限制等等。

## [How] 如何使用 Spring Cloud Gateway 之 Global filter

在 `wang/diff/spring/cloud/gateway` 目录下新建 `config` 目录

创建 `CustomGlobalFilter.java` 类实现 `GlobalFilter`, `Ordered` 接口并在类上增加 `@Configuration` 注解就可以使用过滤功能了。

```
package wang.diff.spring.cloud.gateway.config;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.google.common.collect.Maps;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.Ordered;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Map;

@Configuration
public class CustomGlobalFilter implements GlobalFilter, Ordered {

    private static final String AUTHORIZE_TOKEN = "token";

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String token = exchange.getRequest().getQueryParams().getFirst(AUTHORIZE_TOKEN);
        if (null == token || token.isEmpty()) {
            // 封装错误信息
            Map<String, Object> responseData = Maps.newHashMap();
            responseData.put("code", 401);
            responseData.put("message", "未知的验证信息");

            try {
                // 将信息转换为 JSON
                ObjectMapper objectMapper = new ObjectMapper();
                byte[] data = objectMapper.writeValueAsBytes(responseData);

                // 输出错误信息到页面
                ServerHttpResponse response = exchange.getResponse();
                DataBuffer buffer = response.bufferFactory().wrap(data);
                response.setStatusCode(HttpStatus.UNAUTHORIZED);
                response.getHeaders().add("Content-Type", "application/json;charset=UTF-8");
                return response.writeWith(Mono.just(buffer));
            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```

### 测试

浏览器访问 `http://localhost:9000/nacos-feign-consumer/echo/app-name` 得到如下结果

```
{"code":401,"message":"未知的验证信息"}
```

浏览器访问 `http://localhost:9000/nacos-feign-consumer/echo/app-name?token=test` 得到如下结果

```
Hello Nacos Discovery nacos-feign-consumer port: 8081
```
