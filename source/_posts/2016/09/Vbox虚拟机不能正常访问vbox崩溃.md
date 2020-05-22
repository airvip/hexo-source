---
title: Vbox虚拟机不能正常访问vbox崩溃
date: 2016-09-06 09:32:44
tags:
    - Virtualbox
---

> 将愿望倾入不愿忘却的回忆中…

今天在命令行使用vboxmanage管理虚拟机，结果提示air1.vbox不能没有发现，打开文件夹确实没有，那就解决一下...

<!-- more -->

平时在 vbox 中做些系统级的操作特别方便，因为毫无顾虑，可以大胆随意的操作。

# 异常症状

使用GUI界面的错误提示应该类似是这样子的:

![error_vbox](https://s1.ax1x.com/2020/05/22/YLHDw4.jpg)

上图的报错信息：您的虚拟机的配置文件读取不到，既然读取不到，肯定会报错了。

# 异常解决

打开vbox虚拟机的所在目录

![vbox_dir](https://s1.ax1x.com/2020/05/22/YLHBmF.jpg)

发现多了一个air1.vbox-tmp，而air1.vbox没了，重要的配置文件怎么可以没了呢？当然air1.vbox-prev还是存在的。这就好办了，直接将air1.vbox-prev的后缀名改为air1.vbox

现在重启vbox,幸福总是来的太突然，修复结束。

![vbox_ok](https://s1.ax1x.com/2020/05/22/YLHwOU.jpg)