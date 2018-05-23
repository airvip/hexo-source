---
title: Linux(centos7)下yum安装(lAmp)apache
date: 2016-01-23 14:23:11
tags:
    - Linux
    - Centos
---

> 目的虽有，却无路可循；我们称之为路的，无非是踌躇。

Yum是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

<!-- more -->

Yum全称为 Yellow dog Updater, Modified。

在 centos 中要想安装软件当然用一下包管理工具。yum当然是不二之选。然而yum源在国外，没办法，羞于苦逼的网速限制也只好换一下yum源了。

# 配置过程

现在国内比较稳定的肯定要数163的老姜，当然阿里云是一个新的源，本次我们采用163的yum源

1、打开163的镜像使用帮住，点击直接跳转

``` htm
http://mirrors.163.com/.help/centos.html
```

2、按照使用说明先备份一下/etc/yum.repos.d/CentOS-Base.repo

``` html
[root@localhost ~]# mv CentOS-Base.repo CentOS-Base.repo.backup
```

3、下载对应版本repo文件, 放入/etc/yum.repos.d/(操作前请做好相应备份)

* 3.1 在163镜像帮助点击直接下载，然后上传到/etc/yum.repos.d该目录下。
* 3.2 在/etc/yum.repos.d该目录下运行下面的命令，当然你可能没用安装wget,还是用3.1方法吧

``` bash
wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

4、运行一下命令清除缓存

``` bash
[root@localhost yum.repos.d]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: base extras updates
Cleaning up everything
```

5、重新生成缓存，当出现一下结果yum源就更换成功

``` bash
[root@localhost yum.repos.d]# yum makecache
Loaded plugins: fastestmirror
base    | 3.6 kB  0
Determining fastest mirrors
Metadata Cache Created
```

<span style="color:red">备注：当运行yum makecache如果报下面的错误</span>

这里还有一大堆的提示文字被省略

failure: repodata/repomd.xml from base: [Errno 256] No more mirrors to try.

http://mirrors.163.com/centos/7/os/x86_64/repodata/repomd.xml: [Errno 14] curl#6 - "Could not resolve host: mirrors.163.com; Unknown error"

解决测试

| ping domain（域名）	|  ping IP |
|---- |----|
|ping mirrors.163.com |ping 123.58.173.186通    （mirrors.163的ip）|
|ping www.baidu.com   不通  |ping 61.135.169.125通   （百度的一个ip）|
也就是说不能解析域名，去配置一下自己的DNS.

修改dns之后记得重启网络服务