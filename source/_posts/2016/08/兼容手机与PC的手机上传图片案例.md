---
title: 兼容手机与PC的手机上传图片案例
date: 2016-08-01 23:52:12
tags:
    - Javascript
    - 图片上传
---

> 干燥的冷气，尘埃的味道，我在其中…踏上旅途。

头像上传是几乎每个应用都需要的一个功能，但是对于前端来说，写出一个用户体验比较好的又非常不易，从而导致，要么去找插件，要么就原生file上传，体验很是不好，在此做一个demo,方便工作需要。

<!-- more -->

# 基础文件

- hammer.js
- iscroll-zoom.js
- jquery.js
- jquery-photoClip.js

# 编程实现

``` Html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <style>
       *{ 
           margin: 0;padding: 0;
           font-family: 'Microsoft Yahei', '微软雅黑', '宋体', \5b8b\4f53, Tahoma, Arial;
           -webkit-font-smoothing: antialiased;  
        }
        /*main*/
        .main{position:relative;top:0;left:0;  }
        #clipArea{width : 100%;height : 300px;}
        .uploadImg,#clipBtn,#file{
            position:absolute;
            margin-top: 20px; 
            color:#fff; 
            font-size:16px;  
        }
        .uploadImg{
            width : 100px;
            height: 35px;
            background: #FF658E;
            left : 20px;
            border-radius: 5px;
            text-align: center;
            line-height: 35px;
        }
        #clipBtn{ 
            width : 100px;
            height: 35px;
            background: #FF658E;
            border: none;
            border-radius: 5px;
            right:20px;
        }
        #file{left : 20px;width : 132px;height : 35px;opacity: 0;}
        #view{ width : 100%;height : 300px;}
        #old-img{width: 100%;height: 100%;}
    </style>
    <script>
        (function (doc, win) {
            var docEl = doc.documentElement,
            isIOS = navigator.userAgent.match(/iphone|ipod|ipad/gi),
            dpr = isIOS? Math.min(win.devicePixelRatio, 3) : 1,
            dpr = window.top === window.self? dpr : 1, 
            dpr = 1,
            scale = 1 / dpr,
            resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize';
            docEl.dataset.dpr = win.devicePixelRatio;
            if(navigator.userAgent.match(/iphone/gi) && 
                screen.width == 375 && win.devicePixelRatio == 2)
            {
                docEl.classList.add('iphone6')
            }
            if(navigator.userAgent.match(/iphone/gi) && 
                screen.width == 414 && win.devicePixelRatio == 3)
            {
                docEl.classList.add('iphone6p')
            }
            var metaEl = doc.createElement('meta');
            metaEl.name = 'viewport';
            metaEl.content = 'initial-scale=' + scale + 
            ',maximum-scale=' + scale + ', minimum-scale=' + scale;
            docEl.firstElementChild.appendChild(metaEl);
            var recalc = function () {
                var width = docEl.clientWidth;
                if (width / dpr > 640) {
                    width = 640 * dpr;
                };
            };
            recalc()
            if (!doc.addEventListener) return;
         })(document, window);
    </script>
    <title>头像上传|修改</title>
</head>
<body>
<main class="main">
    <div id="clipArea"><img id="old-img"  src="旧的头像路径"></div>
    <div class="uploadImg">选择</div>
    <input type="file" id="file">
    <button id="clipBtn">上传</button>
</main>

<div id="con"></div>
<img id="img" src=""/>

<script src="你的js目录/jquery-2.1.1.min.js"></script>
<script src="你的js目录/js/avatar/hammer.js"></script>
<script src="你的js目录/js/avatar/iscroll-zoom.js"></script>
<script src="你的js目录/js/avatar/jquery.photoClip.min.js"></script>
<script>
$("#clipArea").photoClip({
    width: 200, 
    height: 200,
    file: "#file", 
    ok: "#clipBtn",
    outputType: "jpg",
    strictSize: false,
    loadStart: function(file) {
        $('#old-img').remove();
    },
    loadComplete: function(src) {},
    loadError: function(event) {},
    clipFinish: function(dataURL) {
        $.ajax({
            type: "POST",
            url: "?g=Wap&c=My&a=upload",
            data: 'img='+dataURL,
            dataType:'json',
            success:function(data){
                if(1 == data.code){
                    alert("上传成功" );
                    top.location='要跳转的地址';
                }else{
                    alert("上传失败：原因:" + data.mess);
                    return;
                }
            }
        })
    }
});
</script>
</body>
</html>
</html>
```

效果图
![效果图](/img/201608/upimg/result.png)

手机端支持手势缩放来调整裁剪区域，PC端支持滑轮滚动缩放来调整裁剪区域。

下面是后端用 php 处理的核心代码：请注意以下两点

1. 打算将图片写入的目录要有写权限
2. 生产中的版本最好做个函数检测目录存不存在，不存在先创建

``` php
//接收的文件格式是base64编码的
/*$base64_image = "data:image/png;base64,
iVBORw0.......................G713Sy9pfQ00Qvyf8PoPCl3Wp6oPYAAAAASUVORK5CYII=";*/
if(IS_POST){
    $base64_image = str_replace(' ', '+',  I('img'));
    if(preg_match('/^(data:\s*image\/(\w+);base64,)/', $base64_image, $result)){
    $image_name = uniqid().'.'.$result[2];
    $image_file = "./Upload/{$image_name}";
        if (file_put_contents($image_file,base64_decode(str_replace($result[1],
        '',$base64_image)))){
            $mess  =  $image_name;
        }else{
            $mess   = '文件写入失败';
        }
    }else{
        $mess   = '接收源错误';
    }
        $data = array('code'=>0,'mess'=>$mess);
        echo json_encode($data,true);exit;
}

```
案例到此结束，今天也即将结束，我也就洗洗睡了，大家晚安。