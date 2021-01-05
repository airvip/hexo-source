---
title: XXX不是内部或外部命令,也不是可运行的程序
date: 2016-10-31 01:23:12
tags:
    - Windows
---

> 贫乳是社会地位的象征,是具有稀有价值的！

windows 系统电脑在运行 CMD 命令提示符窗口，出现不是内部或外部命令也不是可运行的程序或批处理。

<!-- more -->

一般情况下是因为没有配置环境变量，只需为系统添加环境变量就可以。

# 解决方法

第一步:开始->计算机（右击）->属性   ||  桌面->计算机（右击）->属性

![desktop](https://s3.ax1x.com/2021/01/05/sFg6mD.jpg)

第二步:选择高级系统设置

![sys_setting](https://s3.ax1x.com/2021/01/05/sFgRkd.jpg)

第三步:环境变量

![env](https://s3.ax1x.com/2021/01/05/sFgc0e.jpg)

第四步:修改（编辑）path路径

![path](https://s3.ax1x.com/2021/01/05/sFggTH.jpg)

第五步:打开你要加入系统变量的软件的目录（用php7为例）复制路径

![bin_dir](https://s3.ax1x.com/2021/01/05/sFgwf1.jpg)

第六步:把路径添加到环境变量中（!!!记着添加上小分号）

![add_path](https://s3.ax1x.com/2021/01/05/sFgr6K.jpg)

现在，点击确定保存，关掉多余的窗口。

这样就可以在命令行里面使用相应的程序了

