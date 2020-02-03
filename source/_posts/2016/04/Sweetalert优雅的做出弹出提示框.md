---
title: Sweetalert优雅的做出弹出提示框
date: 2016-04-30 21:50:55
tags:
    - Javascript
---

> 一句话，不足以讲述一个故事，却可以让我们回味那些个动人的瞬间。

SweetAlert是一款不需要jQuery支持的原生js提示框,风格类似bootstrap。它的提示框不仅美丽动人，并且允许自定义，支持设置提示框标题、提示类型、内容展示图片、确认取消按钮文本、点击后回调函数等。

<!-- more -->

原生js的alert太丑，没有丝毫亮点，自己又不是专业的UI，也设计不出漂亮的弹出提示框效果，现在一款小巧玲珑的js插件给大家带来了福音。它就是Sweet-alert.

[在Github上下载SweetAlert](https://github.com/t4t5/sweetalert)

# 效果对比
![bj](https://s2.ax1x.com/2020/02/03/1UZ9tf.png)

# 基础使用

引入 sweetalert
<script src="your_path/sweetalert.min.js"></script>
<link rel="stylesheet" type="text/css" href="your_path/sweetalert.css">

使用方法

* 最基本的弹窗
```
swal("Hello world!");
```

* 带有错误提示的弹窗
```
swal("Oops...", "Something went wrong!", "error");
```

* 带有成功提示的弹窗
```
swal("Oops...", "you are right!", "success");
```

* 需要确认的弹窗
```
swal({
  title: "Are you sure?",
  text: "You will not be able to recover this imaginary file!",
  type: "warning",
  showCancelButton: true,
  confirmButtonColor: "#DD6B55",
  confirmButtonText: "Yes, delete it!",
  closeOnConfirm: false,
  html: false
}, function(){
  swal("Deleted!",
  "Your imaginary file has been deleted.",
  "success");
});
```

* 自定义弹出
```
swal({
    title: "Good!",
    text: '自定义<span style="color:red">图片</span>、<a href="#">HTML内容</a>。<br/>5秒后自动关闭。',
    imageUrl: "images/thumbs-up.jpg",
    html: true,
    timer: 5000,
    showConfirmButton: false
});
```

* 确认输入弹窗
```
swal({
    title: "输入框来了",
    text: "这里可以输入并确认:",
    type: "input",
    showCancelButton: true,
    closeOnConfirm: false,
    animation: "slide-from-top",
    inputPlaceholder: "填点东西到这里面吧"
}, function(inputValue) {
    if (inputValue === false)
        return false;
    if (inputValue === "") {
        swal.showInputError("请输入!");
        return false
    }
    swal("棒极了!", "您填写的是: " + inputValue, "success");
});
```
