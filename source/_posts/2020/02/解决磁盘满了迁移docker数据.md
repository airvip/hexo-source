---
title: 解决磁盘满了迁移docker数据
date: 2020-02-06 11:18:56
tags: 
    - Docker
---

> 不只有脚下的路，还有远方的天空

安装了 docker 的服务器，运行了一段时间后，发现服务器磁盘(系统盘)快满了。可以通过 `df -h`查看磁盘的使用情况，使用 `du -h --max-depth=1` 层层筛选目录文件过大的目标。最终找到 `/var/lib/docker` 占用了非常大的空间，所以要进行数据迁移到更大的磁盘下面。

**！！！非常重要，转移数据修改docker默认存储位置！！！**
**推荐**：docker 安装完后，第一时间修改 docker 默认存储位置为其他大目录或者磁盘中。规避迁移数据过程中造成的风险。

<!-- more -->

## 迁移流程

* 停止docker服务
```
[root@localhost ~]# systemctl stop docker
```

* 创建新的 docker 目录
通过 `df -h`, 发现 `/dev/mapper/centos-home  总共 350G  /home` 比较不错
在 /home 目录下面建了 /home/modules/docker/lib 目录

```
[root@localhost ~]# mkdir -p /home/modules/docker/lib
```
* 迁移 /var/lib/docker 目录下面的文件到 /home/modules/docker/lib
迁移后的完成docker路径：/home/modules/docker/lib/docker

```
[root@localhost ~]# rsync -avz /var/lib/docker/ /home/modules/docker/lib/
```

* 配置 /etc/systemd/system/docker.service.d/devicemapper.conf
查看 /etc/systemd/system/docker.service.d 目录及 devicemapper.conf 是否存在。如果不存在，就新建

```
[root@localhost ~]# mkdir -p /etc/systemd/system/docker.service.d/
[root@localhost ~]# vim /etc/systemd/system/docker.service.d/devicemapper.conf
```
devicemapper.conf添加如下内容：
```
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd  --graph=/home/modules/docker/lib/docker
```

* 重启docker,开启自启

```
systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```

确认Docker Root Dir修改是否已经生效

[root@localhost ~]# docker info
```
...省略...
 Data file: /dev/loop0
 Metadata file: /dev/loop1
 Data loop file: /home/modules/docker/lib/docker/devicemapper/devicemapper/data
 Metadata loop file: /home/modules/docker/lib/docker/devicemapper/devicemapper/metadata
...省略...
Docker Root Dir: /home/modules/docker/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
```
* 启动成功后，再确认之前的镜像是否还在

```
[root@localhost ~]# docker images
REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
registry                                latest              708bc6af7e5e        13 days ago         25.7MB
sonatype/nexus                          latest              8027e6db5d67        2 weeks ago         452MB
konradkleine/docker-registry-frontend   v2                  60d4b91e68fa        2 years ago         266MB
```

确定容器没问题后,即可删除 `/var/lib/docker/` 目录中的文件
