---
title: Froala editor 实现图片上传功能
date: 2016-03-19 10:45:23
tags:
    - Html
    - 富文本编辑器
---

> 正因为生来什么都没有，因此我们能拥有一切。

Froala 是个采用 HTML5 开发的 WYSIWYG 编辑器，在该案例中我们将实现图片上传功能，并提供一些参数配置。

<!-- more -->

# 下载软件包

方式一：
[前往github下载安装包](https://github.com/froala/wysiwyg-editor)
方式二：
[前往官网下载安装包](https://www.froala.com/wysiwyg-editor)


# 编写html代码
[查看更多 Froala 编辑器参数配置](https://www.froala.com/wysiwyg-editor/docs/options)

``` html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width,height=device-height,initial-scale=1.0,maximum-scale=1.0,user-scalable=0">
<meta name="renderer" content="webkit">
<meta name="keywords" content="5到6个关键词" />
<meta name="description" content="这里是描述"/>

<link rel="shortcut icon" href="./public/img/ico_48.ico">
<link rel="stylesheet" type="text/css" href="./public/css/bootstrap.min.css">
<link rel="stylesheet" type="text/css" href="./public/css/font-awesome.min.css">
<!--下面是froala的所需功能的css 可以根据需要决定去留-->
<link rel="stylesheet" type="text/css" href="[你的css路径]/froala_editor.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/froala_style.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/code_view.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/colors.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/emoticons.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/image_manager.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/image.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/line_breaker.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/table.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/char_counter.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/video.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/fullscreen.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/file.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/plugins/quick_insert.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/codemirror.min.css">
<link rel="stylesheet" type="text/css" href="[你的css路径]/themes/red.min.css">
<!--[if lt IE 9]>
<script src="//cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
<script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
<![endif]-->
<title>froala_editor</title>
</head>
<body>
<div class="container">
    <div class="row">
        <div class="col-xs-12">
            <div id="edit">
            </div>
        </div>
    </div>
</div>
<script src="./public/js/jquery-2.1.1.min.js"></script>
<script src="./public/js/bootstrap.min.js"></script>
<!--下面是froala的所需功能的js 可以根据需要决定去留-->
<script src="[你的js路径]/froala_editor.min.js"></script>
<script src="[你的js路径]/codemirror.min.js"></script>
<script src="[你的js路径]/xml.min.js"></script>
<script src="[你的js路径]/plugins/align.min.js"></script>
<script src="[你的js路径]/plugins/code_beautifier.min.js"></script>
<script src="[你的js路径]/plugins/code_view.min.js"></script>
<script src="[你的js路径]/plugins/colors.min.js"></script>
<script src="[你的js路径]/plugins/draggable.min.js"></script>
<script src="[你的js路径]/plugins/emoticons.min.js"></script>
<script src="[你的js路径]/plugins/font_size.min.js"></script>
<script src="[你的js路径]/plugins/font_family.min.js"></script>
<script src="[你的js路径]/plugins/image.min.js"></script>
<script src="[你的js路径]/plugins/file.min.js"></script>
<script src="[你的js路径]/plugins/image_manager.min.js"></script>
<script src="[你的js路径]/plugins/line_breaker.min.js"></script>
<script src="[你的js路径]/plugins/link.min.js"></script>
<script src="[你的js路径]/plugins/lists.min.js"></script>
<script src="[你的js路径]/plugins/paragraph_format.min.js"></script>
<script src="[你的js路径]/plugins/paragraph_style.min.js"></script>
<script src="[你的js路径]/plugins/video.min.js"></script>
<script src="[你的js路径]/plugins/table.min.js"></script>
<script src="[你的js路径]/plugins/url.min.js"></script>
<script src="[你的js路径]/plugins/entities.min.js"></script>
<script src="[你的js路径]/plugins/char_counter.min.js"></script>
<script src="[你的js路径]/plugins/inline_style.min.js"></script>
<script src="[你的js路径]/plugins/save.min.js"></script>
<script src="[你的js路径]/plugins/fullscreen.min.js"></script>
<script src="[你的js路径]/plugins/quick_insert.min.js"></script>
<script src="[你的js路径]/plugins/quote.min.js"></script>
<script src="[你的js路径]/languages/zh_cn.js"></script>
<script>
    $(function(){
        //超大屏幕
        var toolbarButtons   = ['fullscreen', 'bold', 'italic', 'underline', 'strikeThrough', 'subscript', 'superscript', 'fontFamily', 'fontSize', '|', 'color', 'emoticons', 'inlineStyle', 'paragraphStyle', '|', 'paragraphFormat', 'align', 'formatOL', 'formatUL', 'outdent', 'indent', 'quote', 'insertHR', '-', 'insertLink', 'insertImage', 'insertVideo', 'insertFile', 'insertTable', 'undo', 'redo', 'clearFormatting', 'selectAll', 'html'];
        //大屏幕
        var toolbarButtonsMD = ['fullscreen', 'bold', 'italic', 'underline', 'fontFamily', 'fontSize', 'color', 'paragraphStyle', 'paragraphFormat', 'align', 'formatOL', 'formatUL', 'outdent', 'indent', 'quote', 'insertHR', 'insertLink', 'insertImage', 'insertVideo', 'insertFile', 'insertTable', 'undo', 'redo', 'clearFormatting'];
        //小屏幕
        var toolbarButtonsSM = ['fullscreen', 'bold', 'italic', 'underline', 'fontFamily', 'fontSize', 'insertLink', 'insertImage', 'insertTable', 'undo', 'redo'];
        //手机
        var toolbarButtonsXS = ['bold', 'italic', 'fontFamily', 'fontSize', 'undo', 'redo'];
        $('#edit').froalaEditor({
            charCounterCount       : true,//默认
            charCounterMax         : -1,//默认
            language                 : 'zh_cn',
            saveInterval            : 0,//不自动保存，默认10000
            theme                    : "red",
            height                   : "300px",
            toolbarBottom           : false,//默认
            toolbarButtonsMD        : toolbarButtonsMD,
            toolbarButtonsSM        : toolbarButtonsSM,
            toolbarButtonsXS        : toolbarButtonsXS,
            toolbarInline           : false,//true选中设置样式,默认false
            imageUploadMethod       : 'POST',
            imageUploadURL           : './lib/imgUpload.php',
            //imageUploadParam         : 'upImg',
            imageUploadParams        : {id: "edit"},
            placeholderText          : '博客从此开始',
        })
    });
</script>
</body>
</html>
```

# php处理上传图片
``` php
<?php
// 允许上传的后缀名.
$allowedExts = array("gif", "jpeg", "jpg", "png");

// 获取上传图片的文件名，以.分割
$temp = explode(".", $_FILES["file"]["name"]);

// 取上传文件的后缀名
$extension = end($temp);

/*图片的mime在编辑器中已经检查,在服务器端再次检测
面向过程方式取出mime
FILEINFO_MIME_TYPE 自php5.3.0可以使用
在网络里发现下面报错时，请打开php.ini的php_fileinfo扩展，然后重启服务器*/

$finfo = new finfo(FILEINFO_MIME_TYPE);
$mime = $finfo->file($_FILES["file"]["tmp_name"]);

if ((($mime == "image/gif")||($mime == "image/jpeg")||($mime == "image/pjpeg")||($mime == "image/x-png")||($mime == "image/png")) && in_array($extension, $allowedExts)) {
    // 使用微秒并生成哈希值，构造唯一文件名
    $name = sha1(microtime()) . "." . $extension;
    //移动图片到指定目录.
    move_uploaded_file($_FILES["file"]["tmp_name"], "../uploads/" . $name);
    //实例化一个一般类
    $response = new StdClass;
    $response->link = "../edit_demo/uploads/" . $name;
    echo stripslashes(json_encode($response));
}
```

# 测试结果
![测试结果](https://s2.ax1x.com/2020/02/03/1UEUq1.jpg)

[demo代码已经更新到github,点击查看](https://github.com/airvip/edit_demo)

