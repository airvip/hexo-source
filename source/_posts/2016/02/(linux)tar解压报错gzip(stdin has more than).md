---
title: (linux)tar解压报错gzip(stdin has more than)
date: 2016-02-26 09:15:03
tags:
    - Linux
    - Unzip
---

> 只要你愿意，即便你在天涯海角，我都会去追。

今天解压 bt12315.zip 时解压出错，现将解决方法记录下来，作为问题解决的一个参考，方便之后查看.

<!-- more -->

# 错误详细

**解压时报了如下的错误**
``` bash
[root@localhost ~]# tar -zxvf bt12315.zip 
gzip: stdin has more than one entry--rest ignored
tar: Child returned status 2
tar: Error is not recoverable: exiting now
```
tar 命令是调用了 gunzip 命令的，对只有一个压缩内容的文件来解压的时候才用 tar， 而如果压缩包里有多个文件被压缩了，tar 命令不能继续工作。可以采用 unzip 命令去解压。


检测是否安装unzip（哈哈！我没安装。。。）
``` bash
[root@localhost ~]#yum list installed | grep unzip
```

查看源包里是否存在软件
``` bash
[root@localhost ~]# yum list | grep unzip
unzip.x86_64                            6.0-15.el7                     base  
```

安装unzip
``` bash
[root@localhost ~]# yum -y install unzip 
```
![install unzip](https://s2.ax1x.com/2020/02/03/1UEPVP.jpg)

解压文件到当前文件夹
``` bash
[root@localhost ~]# unzip bt12315.zip -d ./
```

# unzip常用命令

常用解压到当前目录
``` bash
[root@localhost ~]# unzip 要解压的压缩包
```

解压到自定目录
``` bash
[root@localhost ~]# unzip 要解压的压缩包 -d 要解压到的目录路径
```

查看压缩包有哪些文件
``` bash
[root@localhost ~]# unzip -l 要解压的压缩包
```

检测压缩包是否损坏
``` bash
[root@localhost ~]# unzip -t 要解压的压缩包
```

不覆盖之前解压的文件包
``` bash
[root@localhost ~]# unzip -n 要解压的压缩包
```

查看文件压缩率
``` bash
[root@localhost ~]# unzip -v 要解压的压缩包
[root@localhost ~]# unzip -v bt12315.zip
Archive:  bt12315.zip
fc673f59c9994bf4a3846ae84e4a91128ecee594
 Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
--------  ------  ------- ---- ---------- ----- --------  ----
       0  Stored        0   0% 08-03-2015 22:21 00000000  bt12315/
    3218  Defl:N     1446  55% 08-03-2015 22:21 117f51a6  bt12315/COPYING
     204  Defl:N      142  30% 08-03-2015 22:21 550507e5  bt12315/CREDITS
   94252  Defl:N    22617  76% 08-03-2015 22:21 dd5879e2  bt12315/README.markdown
```
