---
title: Linux 之链接文件
date: 2017-03-17 18:41:20
tags:
    - Linux
    - Python
---

> “老板豆浆里面多放点糖” “豆浆已经很甜了，还要吗？” “没事，最近过的有点苦”

Ubuntu 16.04 虽然自带了 Python2 与 Python3，但是 Python3 还是停留在 Python3.5.2 时代，如果想使用 Python3.6 的新特性，毫无疑问，需要安装 Python3.6。

<!-- more -->

[PEP 494 - 查看 Python 3.6 发布计划](Python 3.6 Release Schedule)

# 安装Python3.6

1. 添加 Python3.6 安装包

```
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:jonathonf/python-3.6
$ sudo apt-get update
```

2. 安装 Python3.6

```
$ sudo apt-get -y install python3.6
```

3. 修改系统默认 python 版本为 3.6

```
$ cd /user/bin
$ rm python
$ ln -s python3.6m python
```

4. 升级 pip 版本

```
$ python pip install --upgrade pip
```
