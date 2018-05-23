---
title: debian linux系列中apt命令概览
date: 2017-01-14 13:58:01
tags:
    - Linux
    - Ubuntu
    - Apt
---

> 有自己喜欢的事情是非常美好的。

apt 英文全称 Advannced Packaging Tool,是 Debian linux系列的安装包管理工具。鼎鼎大名的 Ubuntu 操作系统就是 Debian 系列的一员。大部分软件的安装、更新、卸载都是通过 `apt` 命令来实现的。

<!-- more -->

# apt 帮助命令

我们直接在命令终端（terminal）中输入 `apt` 或者 `apt --help`即可查看相关命令

``` bash
$ apt
$ apt --help
```

# 软件包搜索

1. 搜索和 `foo` 匹配的软件包
``` bash
$ apt-cache search foo
```

2. 查看 `foo` 包相关信息，例如描述、版本、大小、依赖以及冲突
``` bash
$ apt-cache show foo
```

3. 显示所有可用软件包，以及它们各自的详细信息（会产生很多输出）
``` bash
$ apt-cache dumpavail
```

4. 显示软件包记录
``` bash
$ apt-cache show <软件包名称>
```

5. 列出所有可用于安装的包
``` bash
$ apt-cache pkgnames
```

6. 查看某软件包是否安装
``` bash
$ dpkg -l | grep <软件包名>
```


# 软件包安装

1. 安装软件
``` bash
$ sudo apt-get install <软件包名称>
```

2. 修复依赖损坏
在linux中使用命令 `apt-get install` 或 `dpkg -i` 出现依赖错误,就可以立即使用该命令。
``` bash
$ apt-get -f install
```

# 软件包维护

1. 更新可用软件包列表
只会检查哪些软件包可以更新，并不会对这些可更新的软件包进行安装更新。
``` bash
$ sudo apt-get update
```

2. 更新已安装的软件包
该命令一般会配合 `apt-get update` 来使用，先看哪些软件可以更新，在执行该命令进行安装更新。
当我们新安装了系统之后，就会进行该步操作。
``` bash
$ sudo apt-get upgrade
```

3. 将系统升级到新版本
``` bash
$ sudo apt-get dist-upgrade
```


# 软件包删除

1. 卸载软件（保留配置文件）
``` bash
$ sudo apt-get remove <软件包名称> 
```

2. 卸载软件（删除配置文件）
``` bash
$ sudo apt-get purge remove <软件包名称> 
```

3. 删除已安装的软件包的.deb安装文件从硬盘中删除掉
如果你的硬盘空间不大的话，可以定期运行这个程序。如果你仍然需要硬盘空间的话，可以试试apt-get clean，这会把你已安装的软件包的安装包也删除掉，当然多数情况下这些包没什么用了，因此这是个为硬盘腾地方的好办法。
``` bash
$ sudo apt-get autoclean
```

4. 删除包缓存中的所有包
类似 `apt-get autoclean` 的命令，但它删除包缓存中的所有包。这是个很好的做法，因为多数情况下这些包没有用了。
``` bash
$ sudo apt-get clear
```

5. 删除为了满足其他软件包的依赖而安装的，但现在不再需要的软件包
``` bash
$ sudo apt-get autoremove
```

# 扩展：apt 与 apt-get
* apt 与 apt-get 都是 Ubuntu 下常用的安装软件的命令
* 早期使用 apt-get,从 Ubuntu 16 开始，官方建议使用 apt
* 这两个命令的使用方式非常相似