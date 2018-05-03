---
title: yum常用命令解析
date: 2016-02-04 01:02:03
tags:
    - Yum
    - Centos
---

> 如果我爱你等于你爱我就好了...

包管理工具来管理linux软件包，快速，简便，节省大量宝贵的时间。yum包已经集成在centos7中，我们直接使用就好。

## yum 常用命令

查看某软件是否已经安装,
```
[root@localhost ~]# yum list installed | grep mysql
[root@localhost ~]# yum list installed | grep gzip
gzip.x86_64                        1.5-7.el7                           @anaconda
```

卸载安装的软件
```
[root@localhost ~]# yum -y remove gzip.x86_64
```

查看yum库上的mysql版本信息
```
[root@localhost ~]yum list | grep mysql 或 yum -y list mysql*
```

使用yum安装多款软件包
```
[root@localhost ~]yum -y install mysql-server mysql mysql-devel 
```

查看yum install安装软件的路径
```
[root@localhost ~]# rpm -ql wget
```

清除header
```
[root@localhost ~]yum clean headers 
```

清除下载的rpm包
```
[root@localhost ~]yum clean packages  
```

清除header与rpm包
```
[root@localhost ~]yum clean all  
```

重新生成缓存
```
[root@localhost ~]yum makecache
```

更新指定程序包package
```
[root@localhost ~]yum update package名 
```