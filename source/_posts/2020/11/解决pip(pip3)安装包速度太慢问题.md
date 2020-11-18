---
title: 解决pip(pip3)安装包速度太慢问题
date: 2020-11-18 14:35:21
tags: 
    - Python
    - Pip
---

> 虽然条条大路通罗马，但是我就出生在罗马。

pip 在工作时，会从国外服务器下载要安装的软件，由于国际 Internet 线路带宽有限，会发现 pip 安装软件速度较慢，还经常因为网络不好，出现中途安装失败的情况。

<!-- more -->

安装包所花时间太慢，对于职场老司机来说肯定是非常开心的一件事(懂的自然懂)。有时候也是需要快速安装，都是临时使用国内的镜像源，但对于初学者，肯定是越快越好。

## 临时解决

`pip3 install -i 国内镜像地址 包名`

例如：从阿里云镜像地址安装 virtualenv

```
pip3 install -i http://mirrors.aliyun.com/pypi/simple/ virtualenv
```

## 永久解决

windows

1. 在用户根目录下创建 pip 文件夹(一般是'C:\Users\你的Windows系统账号名\',或者直接打开 cmd，就是该用户的根目录了)
2. 进入 pip 文件夹，创建 pip.ini 文件(推荐用 vscode,notepad++ 等打开处理)
3. 写入如下内容

```
[global]
index-url=http://mirrors.aliyun.com/pypi/simple
[install]
trusted-host=mirrors.aliyun.com
```

Linux

1. `cd ~`
2. `mkdir .pip && cd .pip`
3. `vim pip.conf` 写入如下内容

```
[global]
index-url=http://mirrors.aliyun.com/pypi/simple
[install]
trusted-host=mirrors.aliyun.com
```

## 国内其他 pip 源

```
清华：https://pypi.tuna.tsinghua.edu.cn/simple
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
华中理工大学：http://pypi.hustunique.com/
山东理工大学：http://pypi.sdutlinux.org/
豆瓣：http://pypi.douban.com/simple/
```