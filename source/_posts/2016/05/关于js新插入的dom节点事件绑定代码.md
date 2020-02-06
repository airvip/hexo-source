---
title: 关于js新插入的dom节点事件绑定代码
date: 2016-05-22 19:14:14
tags:
    - Javascript
---

> 管他不会说话还是不会交流，只要不放弃就一定能把意思传达给对方。

推荐使用on方法来绑定事件，尤其是在对于一些使用js操作之后添加的dom节点要绑定事件是经常要用到的操作。所以现在将demo记录下来，以便之后查看。

<!-- more -->

# dom绑定事件

on()方法用来在选择元素上绑定一个或多个事件的事件处理函数。

on()方法绑定事件处理程序到当前选定的jQuery对象中的元素。在jQuery 1.7中，.on()方法 提供绑定事件处理程序所需的所有功能。帮助从旧的jQuery事件方法转换，see .bind(), .delegate(), 和 .live(). 要删除的.on()绑定的事件，请使用.off()。

html代码
``` Html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="UTF-8">
	<link rel="shortcut icon" href="./public/img/ico_48.ico">
	<link rel="stylesheet" type="text/css" href="./public/css/bootstrap.min.css">
	<title>事件绑定demo</title>
</head>
<body>
    <div id="add">
        <div class="alert alert-success" role="alert">点击再次插入</div>
    </div>
    
    <script src="./public/js/jquery-2.1.1.min.js"></script>
    <script>
        $('.alert').on('click',function(){
            var _html = '<div class="alert alert-success" role="alert">点击再次插入</div>';
            $('#add').append(_html);
        });	
    </script>
</body>
</html>
```
![dom no js](https://s2.ax1x.com/2020/02/06/1yJjMT.jpg)


绑定父级元素来实现，对新添加的dom绑定事件
``` Js
$(function(){
    $('#add').on('click','.alert',function(){
        var _html = '<div class="alert alert-success" role="alert">点击再次插入</div>';
        $('#add').append(_html);
    });
});	
```
![dom has js](https://s2.ax1x.com/2020/02/06/1yJvsU.jpg)


