---
title: windows系统下安装telnet工具
date: 2019-05-22 01:00:13
tags: 
    - Telnet
---

> 很累对吧？舒服是留给死人的！

很多开发人员都是使用 windows 操作系统，当在连接远程客户端或者测试网络时，经常用到 telnet 工具，但是，telnet 工具在 windows 上默认是没有安装的，于是我们就要燥起来。

<!-- more -->

## 安装telnet

1.在 windows 任意位置按 `win + R` 组合键，打开运行并输入 `control` 回车，进入控制面板

![control](/img/201905/telnet_install/control.png)

2.点击`程序和功能`菜单，进入后，在左侧导航位置选择点击 `启用或关闭 Windows 功能`

![program_set](/img/201905/telnet_install/program_set.png)

3.找到 `Telnet 客户端` 进行选中，再点 `确定` 按钮，进行安装直到结束。
![program_set](/img/201905/telnet_install/install.png)

## 测试

我本机有个 tcp 服务，我可以直接测试，打开 cmd, 输入 `telnet 127.0.0.1 7272` 如图

![telnet_test](/img/201905/telnet_install/telnet_test.png)

测试结果

![telnet_result](/img/201905/telnet_install/telnet_result.png)



