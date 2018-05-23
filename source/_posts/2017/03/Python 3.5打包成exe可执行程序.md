---
title: Python 3.5打包成exe可执行程序
date: 2017-03-09 14:44:05
tags:
    - Python
---

我们安装的 Python3.5 已经自带了 pip 包(模块)管理器，我们可以通过 pip 非常方便的安装我们需要的包(模块)，如果想要把我们的程序打包成 exe 文件，我们可以使用 pyinstaller 来进行打包。

<!-- more -->

当前操作系统 
系统版本 windows10
系统类型 64位操作系统，基于x64的处理器

# pyinstaller

pyinstaller 是一个十分好用的第三方包(模块)，它可以在 Windows, Linux, Mac OS X, FreeBSD, Solaris and AIX 等平台下将 Python 源文件打包，通过对源文件打包，Python 程序可以在没有安装 Python 的环境中运行，也可以作为一个独立文件方便传递和管理。

[pyinstaller官方地址](http://www.pyinstaller.org/#)
[pyinstaller 在 GitHub 的仓库](https://github.com/pyinstaller/pyinstaller/)

安装pyinstaller

方式一：直接通过包管理器安装

``` bash
pip install pyinstaller
```

方式二：在github上下载源码安装

``` bash
> cd E:\pyinstaller # 进入下载目录
> python setup.py install # 开始安装
```

# 源码打包成exe

workspace 目录结构

```
| - workspace
  | -- love.py 
  | -- love.ico
```

进入我们 python 程序源文件目录进行打包

```
E:\workspace> pyinstaller -F -c --icon=love.ico love.py
```

![dist](/img/201703/python_exe/dist.png)

常用参数说明

* –icon=图标路径
* -F 打包成一个exe文件
* -w 使用窗口，无控制台
* -c 使用控制台，无窗口
* --clean 清理打包过程中的临时文件
* -D 创建一个目录，里面包含exe以及其他一些依赖性文件
* -v, --version 查看pyinstaller版本

更多参数说明可通过 `pyinstaller -h` 查看
