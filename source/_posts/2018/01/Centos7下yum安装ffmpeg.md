---
title: Centos7下yum安装ffmpeg
date: 2018-01-11 01:00:37
tags:
    - Centos
    - Linux
    - ffmpeg
---

> 我们在年少时并不知道，有些乐章一旦开始，唱的就是曲终人散。

因项目需要，为了方便前端能够统一调用，这就需要对音频进行转码，我们决定采用 ffmpeg-php 这个扩展来进行音频处理，所以就需要安装 ffmpeg 这个软件包。

<!-- more -->

ffmpeg 是目前最主要的网站服务器后台转码程序。由于默认的 CentOS 的软件仓库里没有FFmpeg、FFmpeg-PHP，所以安装第三方的软件仓库。

## 安装平台

查看系统信息
``` bash
$ uname -a
Linux iZ2569alptcZ 3.10.0-514.6.1.el7.x86_64 #1 SMP
 Wed Jan 18 13:06:36 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
$ cat /etc/redhat-release
CentOS Linux release 7.3.1611 (Core)
```

## 进行安装

1. 安装顺序
 * ffmpeg 
 * ffmpeg-devel 
 * ffmpeg-php 

2. 安装前提

查看安装 ffmpeg
``` bash
$ yum list installed | grep ffmpeg
```
如果没有安装，查看 yum 源中有没有 ffmpeg 包
``` bash
$ yum list | grep ffmpeg
```
如果不存在，先安装EPEL Release，因为安装需要使用其他的repo源，所以需要EPEL支持
``` bash
$ sudo yum install -y epel-release
```
如果安装出现缺少Code提示，先导入code,再安装EPEL Release
``` bash
sudo rpm –import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
```
安装完成之后，查看软件源是否写入文件成功
``` bash
$ yum repolist 
```
------

安装 Nux-Dextop 源
``` bash
$ sudo rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
```
如果安装出现缺少Code提示，先导入code,再安装 Nux-Dextop
``` bash
$ sudo rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro 
```
安装完成之后，查看软件源是否写入文件成功
``` bash
$ yum repolist 
``` 

## 安装ffmpeg与ffmpeg-devel
``` bash
$ yum list | grep ffmpeg #查看 yum 源中有没有 ffmpeg 包
ffmpeg.x86_64                            2.6.8-3.el7.nux              nux-dextop
ffmpeg-compat.x86_64                     0.6.7-9.el7.nux              nux-dextop
ffmpeg-compat-debuginfo.x86_64           0.6.7-9.el7.nux              nux-dextop
ffmpeg-compat-devel.x86_64               0.6.7-9.el7.nux              nux-dextop
ffmpeg-debuginfo.x86_64                  2.6.8-3.el7.nux              nux-dextop
ffmpeg-devel.x86_64                      2.6.8-3.el7.nux              nux-dextop
$ yum install -y ffmpeg ffmpeg-devel
# 安装完之后查看版本
$ ffmpeg -version
```

## 安装ffmpeg-php
安装 ffmpeg-php扩展，目前有几种方法，直接下载包解压 或者 用git克隆文件。
直接下载包
``` bash
$ wget https://jaist.dl.sourceforge.net/project/ffmpeg-php/ffmpeg-php/0.6.0/ffmpeg-php-0.6.0.tbz2
```
推荐使用git
``` bash
$ git clone https://github.com/tony2001/ffmpeg-php.git #我使用的
```

进入下载的文件夹，开始编译安装
``` bash
cd ffmpeg-php/
```
使用 phpize 动态安装扩展
``` bash
$ /usr/local/php7/bin/phpize # 先调用 phpize
Configuring for:
PHP Api Version:         20151012
Zend Module Api No:      20151012
Zend Extension Api No:   320151012
$ ./configure --with-php-config=/usr/local/php7/bin/php-config # 产生对应平台的 Makefile
$ make # 编译
$ make test # 一般不需要做测试，是因为编译完提示测试下
$ sudo make install # 安装
# 提示 Installing shared extensions: /usr/local/php7/lib/php/extensions/no-debug-non-zts-20151012/ 说明已经安装
$ sudo vim /usr/local/php7/etc/php.ini # 编辑 php.ini ，加入 extension=ffmpeg.so
或者使用 `sudo echo "extension=ffmpeg.so" >> /usr/local/php7/etc/php.ini` 
#重启php-fpm
//重启 
service php-fpm restart 
```

## 常见问题
在执行 `./configure --with-php-config=/usr/local/php7/bin/php-config` 遇到
`configure: error: ffmpeg headers not found. Make sure ffmpeg is compiled as shared libraries using the --enable-shared option`
解决:
先确认安装了ffmpeg-devel,然后把 ffmpeg 软件的 include 路径下的文件复制到 `/usr/include/`
如果不知道 ffmpeg 软件的 include 路径可以在命令行输入 `ffmpeg -version` 来看 `configuration:` 中有  `--incdir=/usr/include/ffmpeg` 
执行如下命令
``` bash
$ sudo cp -r /usr/include/ffmpeg/* /usr/include/
```

在执行 `make` 遇到 `make: *** [ffmpeg_movie.lo] Error 1`
解决:
``` bash
$ mv ffmpeg_movie.loT ffmpeg_movie.lo
```

在执行 `make` 遇到 `make: *** [ffmpeg_frame.lo] Error 1`
解决:
``` bash
$ mv ffmpeg_frame.loT ffmpeg_frame.lo
```

在执行 `make test` 遇到
```
+-----------------------------------------------------------+
|                       ! ERROR !                           |
| The test-suite requires that proc_open() is available.    |
| Please check if you disabled it in php.ini.               |
+-----------------------------------------------------------+
```
解决:
``` bash
sudo vim /usr/local/php7/etc/php.ini
# 找到 disable_functions，添加 ；注释掉
;disable_functions = passthru,exec,system,chroot,chgrp,chown,proc_open,proc_get_status,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,fsocket,popen
# 去掉 proc_open 改为下面代码
disable_functions = passthru,exec,system,chroot,chgrp,chown,proc_get_status,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,fsocket,popen
```

## 重要说明
由于Nux Dextop仓库可能会与其他第三方库有冲突，比如（Repoforge和ATrpms）。所以，建议默认情况下不启用 Nux Dextop 仓库。

打开`/etc/yum.repos.d/nux-dextop.repo`，将"enabled=1" 修改为 "enabled=0"。
``` bash
$ sudo vi /etc/yum.repos.d/nux-dextop.repo
```
当需要使用Nux Dextop仓库时，显式启用仓库。
``` bash
$ sudo yum --enablerepo=nux-dextop install -y <软件包名> 
```
