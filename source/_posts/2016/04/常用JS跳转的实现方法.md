---
title: 常用JS跳转的实现方法
date: 2016-04-08 11:05:59
tags:
    - Javascript
---

> 越是困难，越要抬起头，地上可找不到任何希望！

本篇文章主要总结在前端开发中使用 js 进行跳转的一些常用方式，方便自己以后使用,最后一种是埋藏在head头信息中.

<!-- more -->

## 页面跳转

top.location的方式
``` Js
top.location='http://www.dear521.com';  
```

 window.location.href方式
``` Js
window.location.href="http://www.dear521.com";
```

 window.navigate方式跳转
``` Js
window.navigate("http://www.dear521.com");
```

window.loction.replace方式实现页面跳转，注意跟第一种方式的区别
``` Js
window.location.replace("http://www.dear521.com");
```
说明
当存在3个php页面（1.php, 2.php, 3.php）
此时停留在 1.php 页面，点击链接进入 2.php 页面的时候，
在 2.php 里面使用 `window.location.replace("3.php")`与用`window.location.href ("3.php")`进入 3.php 页面;
从用户界面来看是没有什么区别的，但是当在 3.php 页面有一个"返回"按钮，调用 `window.history.go(-1)`或者`window.history.back()`方法的时候，点击这个返回按钮想要返回 2.php 页面的时候，
区别就出来了，当用 `window.location.replace("3.php")`;跳转到 3.php 页面的话，3.php 页面中返回按钮调用 `window.history.go(-1)`或者`window.history.back()`方法是不好用的，会返回到1.php。

self.location方式实现页面跳转，和top.location有小小区别
``` Js
self.location='http://www.dear521.com';
```
window.history.back
``` Js
alert("返回");    
window.history.back(-1);
```
meta方式实现跳转(content = 3 单位是秒)
``` Html
<meta http-equiv=refresh content=时间3;URL="目标地址">
```