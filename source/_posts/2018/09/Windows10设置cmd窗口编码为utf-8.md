---
title: Windows设置cmd窗口编码为utf-8
date: 2018-09-09 11:03:55
tags: 
    - Windows
---

> 回避现实的人，未来将更不理想。

有时候某些工作不得不在 Windows 的 cmd 中进行，然而 cmd 的默认编码是 936（ANSI/OEM-简体中文 GBK），当运行的程序中打印的结果使用了非 GBK 编码的字符文字时就会导致在 cmd 的屏幕中乱码横生，改变这种状况要做的就是修改使用的编码。

<!-- more  -->

由于现在的编程中一般使用 utf-8 编码，因此我们一般会把编码设置为 utf-8。

查看现在 cmd 窗口的编码可以先打开 cmd 窗口>右击导航栏>选择属性>在选项标签页中有一个【当前代码页】，即为所使用的编码。

# 设置cmd编码为utf-8

## 临时修改

只想对当前窗口有效，关闭此次 cmd 窗口之后还原默认设置。

在打开的 cmd 窗口中直接输入 `chcp 65001`，然后回车，执行完成之后，cmd 的编码格式就是 utf-8 了。

![cmd2utf8](/img/201809/cmd_code/cmd2utf8.jpg)

## 永久修改

修改注册表，就可以永久修改 cmd 窗口编码。

通过 `windows键 + R 键` 打开运行窗口，输入 `regedit` 回车确定就可以打开注册表编辑器。

依次找到 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor`,然后“右键-新建”，选择“字符串值”，“名称”列填写“autorun”, 并将数值数据修改为“chcp65001” 确定即可。

![regedit_add_item](/img/201809/cmd_code/regedit_add.jpg)

添加成功后如下图所示

![regedit_over](/img/201809/cmd_code/edit_over.jpg)
