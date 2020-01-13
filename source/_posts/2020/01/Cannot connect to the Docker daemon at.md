---
title: Cannot connect to the Docker daemon at...
date: 2020-01-13 15:19:03
tags: 
    - Centos
    - Docker
---

> 隐约雷鸣，阴霾天空，即使天无雨，我亦留此地。

在新的 Centos7 上新安装了 Docker-CE 后，查看 `docker version` 报错 Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?


<!-- more -->

# 系统环境

``` Bash
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-123.el7.x86_64 #1 SMP Mon Jun 30 12:09:22 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]#
[root@localhost ~]# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)
```

# 查看 docker 版本

错误信息如下

```
[root@localhost ~]# docker version
Client:
 Version:           18.09.5
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        e8ff056
 Built:             Thu Apr 11 04:43:34 2019
 OS/Arch:           linux/amd64
 Experimental:      false
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

# 解决

``` bash
[root@localhost ~]# systemctl daemon-reload
[root@localhost ~]# sudo service docker restart
Redirecting to /bin/systemctl restart docker.service
[root@localhost ~]# sudo service docker status
Redirecting to /bin/systemctl status docker.service
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2020-01-13 15:24:01 CST; 3s ago
     ...
[root@localhost ~]# docker version
Client:
 Version:           18.09.5
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        e8ff056
 Built:             Thu Apr 11 04:43:34 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.5
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.8
  Git commit:       e8ff056
  Built:            Thu Apr 11 04:13:40 2019
  OS/Arch:          linux/amd64
  Experimental:     false
```