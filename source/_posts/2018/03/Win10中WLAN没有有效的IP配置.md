---
title: Win10中WLAN没有有效的IP配置
date: 2018-03-05 15:11:18
tags: 
    - Windows
    - Wlan
---

> 知道活着的痛苦处的人就能对人温柔，这和软弱是不一样的。

今天 windows10 系统在连接无线网的时候提示“没有有效的 IP 配置”，但是使用有线连接却可以上网，现在在这里把详细的解决方法记录一下。

<!-- more -->

# 解决流程

1、在开始菜单上单击鼠标右键，选择“命令提示符（管理员）”，如果没有，通过 cortana 搜索 cmd,右键以管理员身份运行，还可以进入C:\Windows\System32，找到cmd.exe，右键管理员身份打开;
![cmd find](https://s1.ax1x.com/2020/04/07/GcC9pt.jpg)

2、在命令提示符框中输入`netsh winsock reset` 按下回车键，在弹出的已重置的提示后重启计算机即可解决问题！
![netsh](https://s1.ax1x.com/2020/04/07/Gc9ztA.jpg)

如果大家也遇到 win10 系统提示“没有有效的 IP 配置”，可以尝试下这样解决。

