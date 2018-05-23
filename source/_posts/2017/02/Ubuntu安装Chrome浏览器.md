---
title: Ubuntu安装Chrome浏览器
date: 2017-02-08 00:10:47
tags:
    - Ubuntu
    - Chrome
---

> 宅，是一种生活状态；死宅，是一种人生境界…

目前为止，我一直认为 chrome 是世界上最好的浏览器，所以在 Linux 下，它也是我必须安装的软件之一。

<!-- more -->

要想使用 chrome 浏览器可以使用命令行的方式进行安装。

# 软件镜像安装

1. 启动终端,输入以下命令,导入谷歌软件的公钥。
``` bash
$ wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```

2. 【谷歌情有独钟患者使用】
将下面的这一行 google 源添加到 /etc/apt/sources.list
``` bash
deb http://dl.google.com/linux/deb/ stable main
```
3. 【中国大网络环境用户使用】
在国内使用上面的 google 下载源会非常慢，所以国内用户可以使用 Linux 公社的源，执行下面的命令,将下载源加入到系统的源列表。
``` bash
$ sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/
```
	如果返回“地址解析错误”等信息，可以百度搜索其他提供 Chrome 下载的源，用其地址替换掉命令中的地址。

4. 更新系统可用软件列表。
``` bash
$ sudo apt-get update
```

5. 安装谷歌 Chrome 浏览器（稳定版）。
``` bash
$ sudo apt-get install google-chrome-stable
```

[上面安装方法的搬运地址传送门](https://deb.pkgs.org/universal/google-amd64/google-chrome-stable_63.0.3239.132-1_amd64.deb.html)


# deb 包安装方式

1. 启动终端，下载 chrome 的 deb 安装包到 Ubuntu 桌面
``` bash
$ wget http://dl.google.com/linux/deb/pool/main/g/google-chrome-stable/
google-chrome-stable_63.0.3239.132-1_amd64.deb
```

2. 安装谷歌浏览器需要使用的软件包依赖
``` bash
$ sudo apt-get install libappindicator1 libindicator7
```

3. 解包安装
``` bash
$ sudo dpkg -i google-chrome-stable_63.0.3239.132-1_amd64.deb 
```

4. 依赖修复
``` bash
$ sudo apt-get -f install
```


