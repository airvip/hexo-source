---
title: Ubuntu安装搜狗输入法
date: 2017-02-08 23:22:37
tags:
    - Ubuntu
    - 输入法
---

> 我相信十年后的八月，我们还能再相遇。

搜狗输入法是基于 fcitx 的。fcitx 被称为 小企鹅输入法，是一个以 GPL 方式发布的输入法平台，可以通过安装引擎支持多种输入法。它的优点是：短小精悍、与程序的兼容性好。

<!-- more -->

## 安装过程

安装搜狗输入法毫无疑问是需要安装 fcitx 依赖的，除此之外，libssh2-1 也是必不可少的依赖。

1. 设置系统输入法，通过 系统设置>语言支持>键盘输入方式系统 然后选择 fcitx 项.

  ![设置系统输入法](/img/201702/sougou/language_set.png)

2. 下载搜狗 deb 安装包
  64位下载
``` bash
$ wget "https://pinyin.sogou.com/linux/download.php?f=linux&bit=64" -O "sougou64.deb"
```
  32位下载
``` bash
$ wget "https://pinyin.sogou.com/linux/download.php?f=linux&bit=32" -O "sougou32.deb"
```
  ![下载搜狗](/img/201702/sougou/sougoudown.png)

3. 开始安装下载好的安装包
``` bash
$ sudo dpkg -i sougou64.deb
``` 
  安装遇到错误是因为有些以来没有安装，我们执行下面的命令修复依赖
``` bash
$ sudo apt install -f
```

4. 直到修复安装完之后，我们重新启动系统
``` bash
$ reboot
```

## 结果检验
![搜狗输入法界面](/img/201702/sougou/sougou_desktop.png)


