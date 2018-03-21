---
title: 网页图片延迟加载jquery插件
date: 2016-05-22 18:36:38
tags:
    - Javascript
---

> 请你,驯养我吧。

jQuery Lazy Load 基于 jQuery 的图片延迟加载插件，在用户滚动页面到图片之后才进行加载。对于有较多的图片的网页，使用图片延迟加载，能有效的提高页面加载速度。

<!-- more -->

## 安装

1、前往 gitHub 直接下载使用，[下载地址](https://github.com/tuupola/jquery_lazyload/)

2、可以使用 yarn 或 npm 来安装 beta 版本。
``` bash
$ yarn add lazyload # 使用 yarn 安装
$ npm install lazyload # 使用 npm 安装
```

## 基础使用

1、将代码引入自己的项目
``` html
//jquery.lazyload.js依赖jquery库
<script src="jquery.js"></script>
<script src="jquery.lazyload.js"></script>
```
2、代码中图片设置如下

1. 真实图片地址写在 data-original 属性中，而 src 属性中的图片换成占位符的图片（例如 1x1 像素的灰色图片或者 loading 的 gif 图片）
2. 添加 class="lazy" 用于区别哪些图片需要延时加载，当然你也可以换成别的关键词，修改的同时记得修改调用时的 jQuery 选择器
3. 添加 width 和 height 属性有助于在图片未加载时占满所需要的空间
``` html
<img class="lazy" data-original="img/example.jpg" width="640" height="480">
```

3、初始化惰性加载图片

``` html
$("img.lazy").lazyload();
```

参数说明
``` html
$("img.lazy").lazyload(
    container:window,
    event:'scroll',
    effect:'show',
    effectspeed:undefined
    data_attribute:'original',
    threshold:0,
    failure_limit:0,
    skip_invisible:true,
    appear:null,
    load:null
);
```

* container:父容器。延迟加载父容器中的图片。
* event	:触发加载的事件 ,如click，hover
* effect:加载使用的动画效果，如 show, fadeIn, slideDown 等 jQuery 自带的效果，或者自定义动画。
* effectspeed:动画时间。作为 effect 的参数使用：effect(effectspeed)
* data_attribute:真实图片地址的 data 属性后缀
* threshold:灵敏度。默认为 0 表示当图片出现在显示区域中的立即加载显示；设为整数表示图片距离 x 像素进入显示区域时进行加载；设为负数表示图片进入显示区域 x 像素时进行加载。
* failure_limit:容差范围。页面滚动时，Lazy Load 会遍历延迟加载的图片，检查是否在显示区域内，默认找到第 1 张不可见的图片时，就终止遍历。因为 Lazy Load 认为图片的排序是与 HTML 中的代码中的排序相同，但是也可能会出现例外，通过该值来扩大容差范围。
* skip_invisible:跳过隐藏的图片。图片不可见时（如 display:none），不强制加载。
* appear:图片加载时的事件 (Function)，有 2 个参数：elements_left（未加载的图片数量）、settings（lazyload 的参数）。
* load:图片加载后的事件 (Function)，有 2 个参数，同 appear 。
