---
title: js常用正则表达式
date: 2016-02-22 17:22:45
tags:
    - Javascript
---

> 你什么时候放下，什么时候就没有烦恼。

正则表达式(regular expression, 常常缩写为RegExp) 是一种用特殊符号编写的模式,描述一个或多个文本字符串。

<!-- more -->

正则表达式不管什么语言都是我们在编程中必不可少的一个重要组成部分。

## 验证邮箱

```
function check_email(str){
    var reg= /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = 'sdqhwzb@163.com';
check_email(str);结果为true
```

## 验证用户名
(验证规则：字母、数字、下划线组成，字母开头，4-16位)

```
function check_nickname(str){
    var reg= /^[a-zA-z]\w{3,15}$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = '_airvip';
check_nickname(str);结果为true
```

## 验证手机号

```
function check_telephone(str){
    var reg= /^((\+86)|(86))?1[345678]\d{9}$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = '13891052193';
check_telephone(str);结果为true
```

## 验证网址

```
function check_url(str){
    var reg= /((https?|ftp|mms):\/\/)?([A-z0-9]+[_\-]?[A-z0-9]+\.)*[A-z0-9]+\-?[A-z0-9]+\.[A-z]{2,}(\/.*)*\/?/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = 'http://www.dear521.com';
check_url(str);结果为true
```

## 验证整数与浮点数

```
function check_number(str){
    var reg= /^[+-]?([0-9]*\.?[0-9]+|[0-9]+\.?[0-9]*)([eE][+-]?[0-9]+)?$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = 6666.6666;
check_number(str);结果为true
```

## 验证整数

```
function check_int(str){
    var reg= /^[-+]?\d*$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = 123;
check_int(str);结果为true
```

## 验证汉字
(只能输入汉字)
```
function check_ch(str){
    var reg= /^[\u0391-\uFFE5]+$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = "你好";
check_ch(str);结果为true
```

## 验证IP
```
function check_ip(str){
    var reg= /^(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])(\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])){3}$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = "127.0.0.1";
check_ip(str);结果为true
```

## 验证日期
```
function check_date(str){
    var reg=  /^\d{4}\/(0?[1-9]|1[0-2])\/(0?[1-9]|[1-2]\d|3[0-1])$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = "2015/02/01";
check_date(str);结果为true
```

## 验证时间
```
function check_time(str){
    var reg=  /^([0-2][0-9]):([0-5][0-9]):([0-5][0-9])$/;
    return  res = reg.test(str) == true ?  true : false;
}	
var str = "23:23:00";
check_time(str);结果为true
```
