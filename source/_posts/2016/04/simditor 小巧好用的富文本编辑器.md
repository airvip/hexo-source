---
title: simditor 小巧好用的富文本编辑器
date: 2016-04-17 16:29:32
tags:
    - 富文本编辑器
---

> All for one，one for all.

Simditor 是团队协作工具 Tower 使用的富文本编辑器。 功能精简，加载快速。在开发该系统中我们也采用了该编辑器.

<!-- more -->

# 下载与使用

第一步：下载编辑器源码包点击一下 

你也可以通过bower 和 npm安装

``` Bash
$ npm install simditor
$ bower install simditor
```

第二步：把文件引入你的应用

``` Html
<link rel="stylesheet" type="text/css" href="[style path]/simditor.css" />
<script type="text/javascript" src="[script path]/jquery.min.js"></script>
<script type="text/javascript" src="[script path]/module.js"></script>
<script type="text/javascript" src="[script path]/hotkeys.js"></script>
<script type="text/javascript" src="[script path]/uploader.js"></script>
<script type="text/javascript" src="[script path]/simditor.js"></script>
```

注:

* Simditor 是基于 jQuery 和 module.js的应用.
* hotkeys.js 用于绑定快捷键
* uploader.js 是与文件上传相关的js. 当你需要上传是，引入即可.

在你项目中使用simditor

``` Html
<textarea id="editor" placeholder="显示内容" autofocus></textarea>

var editor = new Simditor({
  textarea: $('#editor')
  //optional options
});
```

一些默认的options

```
textarea: null
placeholder: ''
defaultImage: 'images/image.png'
params: {}
upload: false
tabIndent: true
toolbar: true
toolbarFloat: true
toolbarFloatOffset: 0
toolbarHidden: false
pasteImage: false
cleanPaste: false
```

关于upload

```
upload:{
  url: ''
  params: null
  fileKey: 'upload_file'
  connectionCount: 3
  leaveConfirm: 'Uploading is in progress, are you sure to leave this page?'
}
```

上传响应

```
{
  "success": true/false,
  "msg": "error message", # optional
  "file_path": "[real file path]"
}
```


