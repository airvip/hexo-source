---
title: Linux 之链接文件
date: 2017-03-01 23:10:05
tags:
    - Linux
---

> 努力不会背叛自己，虽然有时会背叛梦想。

在 Linux 中有没有像 Windows 系统中那样的快捷方式呢？毫无疑问，在 Linux 如此强大的系统中，肯定是有的了，在 Linux 他的名称叫做软连接。

<!-- more -->

Linux 系统中有 **软链接** 和 **硬链接** 两种特殊的文件。
* 软链接:可以看作类似 Windows 的快捷方式，可以让你快速链接到目标档案或目录。
* 硬链接：是通过文件系统的inode来产生新文件名，而不是产生新文件。

我们知道文件都有文件名与数据，这在 Linux 上被分成两个部分：用户数据 (user data) 与元数据 (metadata)。用户数据，即文件数据块 (data block)，数据块是记录文件真实内容的地方；而元数据则是文件的附加属性，如文件大小、创建时间、所有者等信息。在 Linux 中，元数据中的 inode 号（inode 是文件元数据的一部分但其并不包含文件名，inode 号即索引节点号）才是文件的唯一标识而非文件名。文件名仅是为了方便人们的记忆和使用，系统或程序通过 inode 号寻找正确的文件数据块。

## 硬链接

硬链接（hard link）只是在某个目录下新建了一个文件名并且指向了某个 inode 号码而已。

在 Unix/Linux 系统中，每个文件都会占用一个 inode ,文件内容由 inode 的记录来指向。要想读取文件，必须要经过目录记录的文件名来指向到正确的 inode 号码才能读取。也就意味着：文件名只与目录有关，文件内容只与 inode 有关。

那可不可以两个文件名指向同一个 inode 号码呢？毫无疑问，是可以的。硬连接就是这样产生的。

创建硬链接语法
``` bash
#ln 要创建硬链接的源文件 新创建的硬链接文件名
```
实例
``` bash
#查看new.txt文件硬连接数及inode号码
$ ls -il 
总用量 4
283874 -rw-rw-r-- 1 airvip airvip 34 Jan 25 08:15 new.txt
#创建硬链接
$ ln /home/airvip/Desktop/new.txt /home/airvip/Desktop/new_hard
#查看new.txt文件硬连接数及inode号码
$ ls -il
总用量 8
283874 -rw-rw-r-- 2 airvip airvip 34 Jan 25 08:15 new_hard
283874 -rw-rw-r-- 2 airvip airvip 34 Jan 25 08:15 new.txt
```
通过上面的代码实例我们可以发现，两个文件名都连接到 283874 这个 inode 号码，并且文件的属性、权限完全一样，并且第三列的硬连接数有 1 变为了 2，这个字段称为“链接”，意思是有多少个文件名连接到这个 inode 号码。

图例：
![hard link](/img/201703/ln/hard_link.png)

## 硬链接特性

* 文件有相同的 inode 及 data block；
* 只能对已存在的文件进行创建；
* 不能交叉文件系统进行硬链接的创建；
* 不能对目录进行创建，只可对文件创建；
* 删除一个硬链接文件并不影响其他有相同 inode 号的文件。

## 软连接

软连接（symbolic link）就是创建一个独立的文件，只不过这个文件比较特殊，它会让自己的数据块指向它连接的那个文件的文件名。由于只是利用文件来作为指向的操作，所以当源文件被删掉的时候，软连接的文件就会"打不开"，会提示"无法打开某文件"，实际上就是找不到源文件的"文件名"而已。

创建硬链接语法
``` bash
#ln -s 要创建软链接的源文件 新创建的硬软链接文件名
```
实例
``` bash
#创建软连接
$ ln -s /home/airvip/Desktop/new.txt /home/airvip/Desktop/new_symbolic
#查看new.txt文件硬连接数及inode号码
$ ls -il new.txt new_symbolic 
262718 lrwxrwxrwx 1 airvip airvip 28 Jan 25 09:17 new_symbolic -> /home/airvip/Desktop/new.txt
283874 -rw-rw-r-- 2 airvip airvip 34 Jan 25 08:15 new.txt
```
通过上面的代码实例我们可以发现，两个文件指向了不同的 inode 号码，也就意味着他们是两个独立的文件，并且链接文件的数据块内容就是目标源文件的"文件名"，并且不难发现，链接文件的大小为 28 bytes ,这是因为箭头(->)后面的文件名 "/home/airvip/Desktop/new.txt" 总共 28 个英文字母，每个英文字母占用 1 byte,所以总共 28 bytes 了。

图例：
![symbolic link](/img/201703/ln/symbolic_link.png)

## 软连接特性

* 软链接有自己的文件属性及权限等；
* 可对不存在的文件或目录创建软链接；
* 软链接可交叉文件系统；
* 软链接可对文件或目录创建；
* 创建软链接时，链接计数不会增加；
* 删除软链接并不影响被指向的文件，但若被指向的原文件被删除，则相关软连接被称为死链接（即 dangling link，若被指向路径文件被重新创建，死链接可恢复为正常的软链接）。

说明：
对于 **要创建软链接的源文件** 参数记住 **最好使用绝对路径**。
