---
title: 给html元素设置与移除disabled属性
date: 2016-10-13 08:58:21
tags:
    - Html
    - Javascript
    - Css
---

> 正因为生来什么都没有，因此我们能拥有一切。

有时候对某些表单元素禁止使用，我们可以通过使用 disabled 属性实现，有些也可以通过 readonly 属性实现，特定场景下我们可以自由选择使用。

<!-- more -->

表单中 disabled 与 readonly 的区别：

disabled 对所有的表单元素都有效，包括 select, radio, checkbox, input, button 等。
readonly 只针对 input 和 textarea 元素有效。

表单元素在使用了 disabled 属性后，当我们通过 POST 或 GET 的方式提交数据时，使用了 disabled 属性的表单元素不会被传递出去，而使用 readonly 属性的表单元素会被传递出去。

# Js设置disabled属性

代码示例

``` Html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<body>

    <div id="container">
        <form action="#">
            <button id="btn1" class="btn btn-success btn-block">测试1</button>
            <button id="btn2" disabled="disabled" class="btn btn-info btn-block">测试2</button>
        </form>
    </div>

    <script>
        function disableElement(element, val){
            document.getElementById(element).disabled=val;
        }

        disableElement("btn1", true);
        disableElement("btn2", false);
    </script>
</body>
</html>
```

# Jquery设置disabled属性

代码片段

``` Javascript
$(function () {
    $('#btn1').attr("disabled",true); // 禁用方式一
    $('#btn1').attr("disabled","disabled"); // 禁用方式二


    $('#btn2').attr("disabled",false); // 启用方式一
    $('#btn2').removeAttr("disabled"); // 启用方式二
});
```