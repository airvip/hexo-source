---
title: IIS服务器监听端口的更改方法
date: 2016-11-23 13:44:51
tags:
    - IIS
---

> 这一次的离别就应该是永别了吧！

最近要和使用 C# 的同学共用一台服务器，我们的 Nginx 服务器一直占用着 8888 端口，无奈只能给 C# 的同学修改下 IIS 服务器监听的端口。不要问我为啥要改 IIS 服务器的端口，因为服务器在我手里啊...

<!-- more -->

前提是已经安装了 iis7 的 Windows 系统。

# 修改图示

点击开始菜单，打开【控制面板】

![开始](https://s3.ax1x.com/2021/01/05/sF26g0.jpg)

进入控制面板，将控制面板图标切换成【大图标】或者【小图标】

![control](https://s3.ax1x.com/2021/01/05/sF2cvV.jpg)

选择【管理工具】点击进入

![click_manage](https://s3.ax1x.com/2021/01/05/sF22uT.jpg)

在管理工具中选择 【Internet 信息服务(IIS)管理器】

![manage](https://s3.ax1x.com/2021/01/05/sF2hE4.jpg)

选择 【Default Web Site】，在右边操作栏点击停止服务操作

![website](https://s3.ax1x.com/2021/01/05/sF2oCR.jpg)

停止之后选择【绑定】

![server_stoped](https://s3.ax1x.com/2021/01/05/sF2559.jpg)

然后看到弹出的窗口，第一行就是监听 8888 端口，选中后，点击编辑

![port_bind_disp](https://s3.ax1x.com/2021/01/05/sF2WbF.jpg)

现在就可以根据自己的需求进行修改了,修改之后点击确定

![make_sure](https://s3.ax1x.com/2021/01/05/sF2RDU.jpg)

查看已经修改成功，就可以启动服务了

![restart_server](https://s3.ax1x.com/2021/01/05/sF24UJ.jpg)

现在通过浏览器输入 `127.0.0.1:8800` 就可以查看我们的修改结果了

说明：

如果问题没有解决，有可能是 ie 导致的缓存，必须要重新打开 ie 浏览器再输入地址才能够看到效果。






