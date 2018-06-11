---
title: Fiddler抓取指定域名数据包
date: 2018-04-13 18:23:55
tags: 
    - Fiddler
---

> 我决定以人类的一个方法来感激你，我打算以身相许！

我们在使用 Fiddler 抓取数据包的时候，会看到其他软件进程发送的请求，这样非常不利于我们对数据包的搜索与查看，这时候我们只需过滤出我们需要的域名下的数据包就可以。

<!-- more -->

我们可以通过 Fiddler 工具自带的 Filters 过滤非常方便的配置我们想要抓取的特定域名数据。

# 抓取指定域名包

点击 Fiddler 右侧窗口的 Filters 选项卡，，勾选顶部的Use Filters，找到Hosts区域，设置以下三个选项：

![config](/img/201804/fiddler_filter/conf_01.jpg)

选项一：有三个选项

``` Html
-No zone filter-  // 不进行过滤 [我们使用此选项]
Show Only Intranet Hosts // 只显示内网主机
Show Only Internet Hosts // 只显示网络主机
```

选项二：有四个选项

``` Html
-No Host Filter- // 不设置hosts过滤
Hide The Following Hosts // 隐藏过滤到的域名
Show Only The Following Hosts // 只显示过滤到的域名 [我们使用此选项]
Flag The Following Hosts // 标记过滤到的域名
```

选项三：配置我们要抓取的域名
文本框内输入需要过滤的域名即可,如：`*.dear521.com;*.diff.wang;`。
多个域名使用`;`分号分割，fiddler 默认会检查 http 头中设置的 host，强制显示 http 地址中的域名。


我们还做了配置，如果 URL 中包含 JS 或者 CSS 结尾的文件,也进行隐藏操作。

``` Html
REGEX:\.(js|css|js\?.*|css\?.*)$
```

最后点击 Actions > Run Filterset now 即可

![use_filter](/img/201804/fiddler_filter/use_filter.jpg)

# 测试配置

打开浏览器，输入 www.baidu.com, 再输入 blog.diff.wang 查看结果

![test_result](/img/201804/fiddler_filter/test_result.jpg)