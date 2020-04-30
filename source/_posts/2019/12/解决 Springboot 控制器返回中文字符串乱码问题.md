---
title: 解决 Springboot 控制器返回中文字符串乱码问题
date: 2019-12-01 09:06:34
tags: 
    - Java
    - Springboot
    - SpringMVC
---

> 数学和物理，就是把看不见的星星写在了纸上。

在使用 Springboot 做测试用例的时候，我们经常会在 Controller 层直接返回一个 String 类型的数据，当在浏览器访问的时候，尽然是一串 ??? 乱码？ WTF，什么鬼，下面我们开处理下这种匪夷所思的返回信息。

<!-- more  -->

## demo 问题在线

控制器代码

``` Java
package wang.diff.spring.boot.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "欢迎页面";
    }
}
```

浏览器访问

![springmvc01](https://s1.ax1x.com/2020/04/29/JT9Ycj.png)


## 急速解决(局部)

仅处理该测试程序

``` java
    @GetMapping("/hello", produces="text/html;charset=UTF-8")
    public String hello() {
        return "欢迎页面";
    }
```

再次访问已经正常

## 全局解决

新增 MVC 自定义配置

``` java
package wang.diff.spring.boot.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.StringHttpMessageConverter;
import org.springframework.web.servlet.config.annotation.*;

import java.nio.charset.Charset;
import java.util.List;

@Configuration
public class MyMvcConfig extends WebMvcConfigurationSupport {

    @Bean
    public HttpMessageConverter<String> responseBodyConverter() {
        System.out.println("解决控制器返回中文字符串为???问题");
        return new StringHttpMessageConverter(Charset.forName("UTF-8"));
    }

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        converters.add(responseBodyConverter());
        System.out.println("下面必须加载默认转换器，不然bug玩死人，并且该bug目前在网络上 百度，谷歌，各大论坛 似乎没有解决方案");
        addDefaultHttpMessageConverters(converters);
    }

}
```

此时访问已经正常显示返回中文字符串

![springmvc_ok](https://s1.ax1x.com/2020/04/29/JTPv9S.png)

如果还不行(应该不会出现),在 `application.properties` 文件中添加 `server.tomcat.uri-encoding=UTF-8`
