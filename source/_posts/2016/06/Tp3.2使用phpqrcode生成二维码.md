---
title: Tp3.2使用phpqrcode生成二维码
date: 2016-06-13 22:22:28
tags:
    - Php
    - Thinkphp
---

> 慕君之心，至死方休。

好久没有动了，今天整理一下，把使用phpqrcode类生成二维码的简单示例记录下来，方便之后的学习与使用，我是用的thinkphp版本是3.2.3

<!-- more -->

# 添加扩展

1、下载 phpqrcode 类包，[点击在 github 上下载](https://github.com/t0k4rt/phpqrcode)

2、将类包全部放入thinkPHP的vendor，如图
![thinkphp vendor](/img/201606/phpcode/phpvendor.jpg)

# 编程实现

在控制器里新建了一个QrCodeController.class.php控制器文件

php 代码如下
``` Php
<?php
namespace Home\Controller;
use Think\Controller;
class QrCodeController extends Controller {

    //显示生成二维码页面
    public function index(){
        $this->assign('title','二维码在线生成');
        $this->display();
    }

    //生成二维码图片
    public function qrcode(){
        if(!IS_POST)$this->error('非法操作');
        $temp   = I('post.');
        Vendor('phpqrcode.phpqrcode');
        //生成二维码
        $qr = new \QRcode();
        //网址或者文本
        $url    = empty($temp['content']) ? 'http://www.dear521.com' : $temp['content'];
        //容错级别
        $level = intval($temp['level']);
        //生成图片尺寸
        $size    = empty($temp['size']) ? '10':intval($temp['size']);
        //空白边距
        $margin = intval($temp['size']);
        /**
         *  png($text, $outfile = false, $level = QR_ECLEVEL_L, $size = 3, $margin = 4, $saveandprint=false)
         * $text 就是代码里的url
         * $outfile默认为否，不生成文件，只将二维码图片返回，否则需要给出存放生成二维码图片的路径
         * $level默认为L，这个参数可传递的值分别是L(QR_ECLEVEL_L，7%)，M(QR_ECLEVEL_M，15%)，Q(QR_ECLEVEL_Q，25%)，H(QR_ECLEVEL_H，30%)。这个参数控制二维码容错率，不同的参数表示二维码可被覆盖的区域百分比。
         * 利用二维维码的容错率，我们可以将头像放置在生成的二维码图片任何区域。
         * $size，控制生成图片的大小，默认为3
         * $margin，控制生成二维码的空白区域大小
         * $saveandprint，保存二维码图片并显示出来，$outfile必须传递图片路径。
         */
        $path = './Upload/qrcode/'.date('yMd',time()).'_'.rand(1000,9999).'.png';
        createDir(dirname($path));
        $qr->png($url,$path,$level,$size,$margin,false);
        if(file_exists($path)){
            $path = substr($path,1);
            echo json_encode(array('code'=>1,'mess'=>'','data'=>__ROOT__.$path));
        }else{
            echo json_encode(array('code'=>0,'mess'=>'','data'=>''));
        }

    }

}
```

页面表单代码如下

``` Html
<form class="form-horizontal"  method="post" >
    <div class="form-group">
        <label for="size" class="col-xs-2 col-sm-2 col-lg-2 control-label">尺寸大小</label>
        <div class="col-xs-10 col-sm-4 col-lg-4 ">
            <select class="form-control" name="size" id="size">
                <for start="20" end="0" comparison="gt" step="-1" name="i">
                <option value="{$i}" <if condition="$i eq 11">selected="selected</if>>{$i}</option>
                </for>
            </select>
        </div>
    </div>
    <div class="form-group">
        <label for="margin" class="col-xs-2 col-sm-2 col-lg-2 control-label">边距大小</label>
        <div class="col-xs-10 col-sm-4 col-lg-4 ">
            <select class="form-control" name="margin" id="margin">
                <for start="20" end="-1" comparison="gt"   step="-1" name="i">
                <option value="{$i}" <if condition="$i eq 11">selected="selected</if> >{$i}</option>
                </for>
             </select>
        </div>
    </div>

    <div class="form-group">
        <label for="level" class="col-xs-2 col-sm-2 col-lg-2 control-label">容错级别</label>
        <div class="col-xs-10 col-sm-4 col-lg-4 ">
            <select class="form-control" name="level" id="level">
            <option value="L">L - Low (7%)</option>
            <option value="M" selected="selected">M - Medium (15%)</option>
            <option value="Q">Q - Quartile (25%)</option>
            <option value="H">H - High (30%)</option>
            </select>
        </div>
    </div>

    <div class="form-group">
        <label for="content" class="col-xs-2 col-sm-2 col-lg-2 control-label">编码内容</label>
        <div class="col-xs-10 col-sm-4 col-lg-4 ">
            <input type="text" class="form-control" name="content" id="content" placeholder="http://www.dear521.com" value=""/>
        </div>
    </div>

    <div class="form-group">
        <div class="col-xs-12 col-sm-offset-2 col-sm-4 col-lg-offset-2 col-lg-4">
            <button type="button" id="post-data" class="btn btn-default btn-success btn-block">确认生成</button>
        </div>
    </div>

</form>
```

Js 提交请求代码如下
请先引入JQuery库，我是用的是Bootstrap前端框架
``` Js
<script>
    $(function(){
        var url = "{:U('Home/QrCode/qrcode')}";
        $('#post-data').click(function(){
            var content   = $.trim($('#content').val());
            if(content == ''){swal('非法操作','','error'); return false; }
            var size       = $.trim($('#size').val());
            var margin    = $.trim($('#margin').val());
            var level    = $.trim($('#level').val());
            $.ajax({
                type:'post',
                url:url,
                data:{
                    'size':size,
                    'margin': margin,
                    'level':level,
                    'content': content
                },
                dataType:"json",
                success:function(data){
                    if(1 == data.code){
                        $('#qrcode').attr({'src':data.data});
                        return false;
                    }else{
                        swal('操作失败','','error'); return false;
                    }
                },
            });
        });
    });
</script>
```

# 效果图
最终效果图
![result](/img/201606/phpcode/result.jpg)
