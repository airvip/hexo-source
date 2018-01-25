---
title: Linux 之inode的浅显理解
date: 2017-03-01 10:05:36
tags:
    - Linux
---

> 正因为有忘也忘不了的回忆，才会变得坚强，这就是所谓的成长吧！

在 Unix/Linux 系统中，理解inode是非常必要的，有助于我们对文件系统和硬盘存储的学习。理解了 inode 有助于我们把系统复杂的底层抽象成简单的概念。

<!-- more -->

## inode 是什么

文件储存在硬盘上，硬盘的最小存储单位叫做"扇区"（Sector）。每个扇区储存512字节（相当于0.5KB）。
操作系统读取硬盘的时候，如果一个个扇区地读取，这样效率就太低了，而是一次性连续读取多个扇区，即一次性读取一个"块"（block）。这种由多个扇区组成的"块"，是文件存取的最小单位。"块"的大小，最常见的是4KB，即连续八个 sector组成一个 block。
文件数据都储存在"块"中，那么，我们还必须找到一个地方储存文件的元信息，比如：文件的创建者、文件的创建日期、文件的大小等等。这种储存文件元信息的区域就叫做inode，中文译名为"索引节点"。
每一个文件都有对应的inode，里面包含了与该文件有关的一些信息。

## inode 有什么

inode 中存储了除了文件名以外的所有文件信息。

inode 包含文件信息如下：
1. 文件的字节数
2. 文件拥有者的User ID
3. 文件的Group ID
4. 文件的读、写、执行权限
5. 文件的时间戳，共有三个：
   ctime指inode上一次变动的时间;
   mtime指文件内容上一次变动的时间;
   atime指文件上一次打开的时间。
6. 链接数，即有多少文件名指向这个inode
7. 文件数据block的位置

查看某个文件的 inode 信息
``` bash
$ stat 文件名
```
![文件inode信息](/img/201703/inode/inode_stat.png)

## inode 的大小

inode也是需要消耗硬盘空间的，所以硬盘格式化的时候，操作系统自动将硬盘分成两个区域。一个是数据区，存放文件数据；另一个是inode区（inode table），存放inode所包含的信息。

每个inode节点的大小，一般是128字节或256字节。inode节点的总数，在格式化时就给定，一般是每1KB或每2KB就设置一个inode。假定在一块1GB的硬盘中，每个inode节点的大小为128字节，每1KB就设置一个inode，那么inode table的大小就会达到128MB，占整块硬盘的12.8%。

查看每个硬盘分区的inode总数和已经使用的数量，可以使用df命令。
``` bash
$ df -i
```
![inode_dfi信息](/img/201703/inode/inode_dfi.png)

查看每个inode节点的大小
``` bash
$ sudo dumpe2fs -h /dev/xvda1 | grep "Inode size"
[sudo] password for airvip: 
dumpe2fs 1.42.9 (28-Dec-2013)
Inode size:	          256
```

## inode 号码

每个inode都有一个号码，操作系统用inode号码来识别不同的文件。

注意：Unix/Linux系统内部不使用文件名，而使用inode号码来识别文件。对于系统来说，文件名只是inode号码便于识别的别称或者绰号。

用户通过文件名，打开文件。实际上，系统内部这个过程分成三步：
1. 系统找到这个文件名对应的inode号码；
2. 通过inode号码，获取inode信息；
3. 根据inode信息，找到文件数据所在的block，读出数据。

查看文件名对应的inode
``` bash
$ ls -i test.txt 
1066687 test.txt
```

Unix/Linux系统中，目录（directory）也是一种文件。打开目录，实际上就是打开目录文件。
目录文件的结构非常简单，就是一系列目录项（dirent）的列表。每个目录项，由两部分组成：所包含文件的文件名，以及该文件名对应的inode号码。

查看目录对应的inode
``` bash
ls -i ./wwwssh/
1051452 id_rsa.pub
```

参考文章：[inode理解](http://www.ruanyifeng.com/blog/2011/12/inode.html)

