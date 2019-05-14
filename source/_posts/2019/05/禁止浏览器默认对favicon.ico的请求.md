---
title: 禁止浏览器默认对favicon.ico的请求
date: 2019-05-14 09:24:55
tags: 
    - Php
    - Workman
    - 浏览器
---

> 高吗？ 富吗？ 帅吗？ 不？ 还不去撸码！

今天做了个测试，检测当处理的请求数到达 1000 时，就重启一个进程，在浏览器做测试的时候发现每次刷新页面,返回的计数都增加了 2 个，搞什么毛线。

<!-- more  -->

## 计数程序

可以暂时不看这块程序

```
<?php

namespace app;

use Workerman\Worker;

require_once __DIR__.'/../vendor/autoload.php';

define('MAX_REQUEST', 1000);

$worker = new Worker('http://0.0.0.0:2345');

$worker->onMessage = function ($conn, $data){
    //已处理请求数量
    static $request_count = 0;
    $conn->send('hello_'.$request_count);

    // 如果请求数达到 1000
    if($request_count++ >= MAX_REQUEST){
        /*
         * 退出当前进程，主进程会立刻重新启动一个全新进程补充上来
         * 从而完成进程重启
         */
        Worker::stopAll();
    }else{
        echo "$request_count \n";
    }
};

Worker::runAll();

```

计数每次加 2 ，那这样我的检测不就直接减半吗？这肯定是不合适的。于是按下了神奇的 `F12` 查看缘由。

在 Network 里面，WTF，怎么莫名奇妙的出现了一个不该存在的请求？
`http://127.0.0.1:2345/favicon.ico` 这是什么鬼？原来是浏览器自作多情的为我们多请求了该网址的小图标，怪不得搞得我的计数不准了。

## favicon.ico 图标

favicon.ico 图标用于收藏夹图标和浏览器标签上的显示，如果不设置，浏览器会请求网站根目录的这个图标，如果网站根目录也没有这图标会产生 404。出于优化的考虑，要么就有这个图标，要么就禁止产生这个请求。

尤其要注意 360等浏览器，它们在请求 favicon.ico 的时候会忽略端口号的。

问题是我根本就不想产生 favicon.ico 的请求。

于是就需要在程序中做屏蔽

`<link rel="icon" href="data:;base64,=">`

或者详细一点

`<link rel="icon" href="data:image/ico;base64,aWNv">`

## 修改后的计数程序

```
<?php

namespace app;

use Workerman\Worker;

require_once __DIR__.'/../vendor/autoload.php';

define('MAX_REQUEST', 1000);

$worker = new Worker('http://0.0.0.0:2345');

$worker->onMessage = function ($conn, $data){
    //已处理请求数量
    static $request_count = 0;

    $conn->send('<link rel="icon" href="data:;base64,=">hello_'.$request_count);

    // 如果请求数达到 1000
    if($request_count++ >= MAX_REQUEST){
        /*
         * 退出当前进程，主进程会立刻重新启动一个全新进程补充上来
         * 从而完成进程重启
         */
        Worker::stopAll();
    }else{
        echo "$request_count \n";
    }
};

Worker::runAll();
```

## 注意

由于使用的是 dataURL 方式，IE < 8 等老版本的浏览器就不属于咱们的考虑范围了，_φ(❐_❐✧ 人丑就要多读书





