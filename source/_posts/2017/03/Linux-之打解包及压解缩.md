---
title: Linux 之打解包及压解缩
date: 2017-03-04 20:11:05
tags:
    - Linux
---

> 做出一副温柔的样子来折磨人不是更令人难受吗？

利用tar命令，可以把一大堆的文件和目录全部打包成一个文件，这对于备份文件或将几个文件组合成为一个文件以便于网络传输是非常有用的。

<!-- more -->

打包:将一大堆文件或目录变成一个总的文件。
压缩:将一个大的文件通过一些压缩算法变成一个小文件。

# tar 打包与解包

只使用 `tar` 命令我们打包好的文件后缀名一般使用 `.tar`
tar 命令本事只负责打包或解包，并不负责压缩解压缩

语法
``` bash
tar [选项] [参数]
```
常用选项说明

| 选项 | 说明 |
| :---- | :---- |
| -c | 生成档案文件，创建打包文件 |
| -x | 解开档案文件 |
| -v | 列出归档解档的详细过程，显示进度 |
| -f | 指定档案文件名称，f后面一定是 .tar 文件，所以该选项必须当在最后 |
| -C | 解包到指定目录，注意：目录必须存在 |

打包实例
``` bash
#tar -cvf 打包文件.tar  被打包的文件/路径...
$ tar -cvf mylog.tar /var/2017/01* #仅打包不进行压缩
```

解包实例
``` bash
#tar -xvf 打包文件.tar
$ tar -xvf mylog.tar
```

# gzip 压缩解压缩

`tar` 与 `gzip` 命令可以结合使用，来实现对文件打包并压缩的目的，使用 `gzip` 对 `tar` 包进行压缩后的文件扩展名，我们一般使用 `.tar.gz`

在 `tar` 命令中有一个 `-z` 的选项，是用来调用 `gzip` 的，从而方便的实现打包并压缩的功能。

打压缩包实例
``` bash
#tar -zcvf 打包文件.tar.gz  被打包的文件/路径...
$ tar -zcvf mylog.tar.gz /var/2017/01* #打包并压缩
```

解压缩包实例
``` bash
#tar -zxvf 打包文件.tar.gz
$ tar -zxvf mylog.tar.gz

#tar -zxvf 打包文件.tar.gz -C 解压到指定目录
$ tar -zxvf mylog.tar.gz -C ~/Desktop/mylog
```

# bzip2 压缩解压缩

`tar` 与 `bzip2` 命令可以结合使用，来实现对文件打包并压缩的目的，使用 `bzip2` 对 `tar` 包进行压缩后的文件扩展名，我们一般使用 `.tar.bz2`

在 `tar` 命令中有一个 `-j` 的选项，是用来调用 `bzip2` 的，从而方便的实现打包并压缩的功能。

打压缩包实例
``` bash
#tar -jcvf 打包文件.tar.bz2  被打包的文件/路径...
$ tar -jcvf mylog.tar.bz2 /var/2017/01* #打包并压缩
```

解压缩包实例
``` bash
#tar -jxvf 打包文件.tar.bz2
$ tar -jxvf mylog.tar.bz2

#tar -jxvf 打包文件.tar.bz2 -C 解压到指定目录
$ tar -jxvf mylog.tar.bz2 -C ~/Desktop/mylog2
```