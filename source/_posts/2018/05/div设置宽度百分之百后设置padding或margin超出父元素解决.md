---
title: div设置宽度百分之百后设置padding或margin超出父元素解决
date: 2018-05-22 10:34:23
tags: 
    - Html
    - Css
---

> 如果没什么好事发生的话，就把没有发生坏事的每一天当作好事吧。

做测试的时候，发现设置了元素宽度100%之后又设置了 padding 或者 margin,导致元素超出了父元素，解决方法非常简单，只需要使用 CSS3 的 box-sizing 属性就可以解决这个问题，使用该属性可以将padding计算在width内。

<!-- more -->

# 问题编码

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>样式测试</title>
    <style>
        body{
            padding: 0px;
            margin: 0px;
        }
        #container {
            background-color: gray;
            padding: 5px;
        }
        #container .box{
            border: 1px solid #F00;
            background-color: gold;
            padding-left: 10px;
            width: 100%;
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box">innerbox</div>
    </div>
</body>
</html>
```

效果图
![error style](/img/201805/htmlstyle/errorstyle.png)

# box-sizing语法

`box-sizing: content-box|border-box|inherit;`

content-box
这是由 CSS2.1 规定的宽度高度行为。
宽度和高度分别应用到元素的内容框。
在宽度和高度之外绘制元素的内边距和边框。

border-box
元素设定的宽度和高度决定了元素的边框盒,即：为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。
通过从已设定的宽度和高度分别减去边框和内边距才能得到内容的宽度和高度。

inherit
规定应从父元素继承 box-sizing 属性的值。

# 修改编码

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>样式测试</title>
    <style>
        body{
            padding: 0px;
            margin: 0px;
        }
        #container {
            background-color: gray;
            padding: 5px;
        }
        #container .box{
            box-sizing:border-box;
            -moz-box-sizing:border-box; /* Firefox */
            -webkit-box-sizing:border-box; /* Safari */
            border: 1px solid #F00;
            background-color: gold;
            padding-left: 10px;
            width: 100%;
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box">innerbox</div>
    </div>
</body>
</html>
```

效果图
![ok style](/img/201805/htmlstyle/okstyle.png)