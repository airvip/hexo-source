---
title: 常用docker compose模板
date: 2020-01-16 10:49:22
tags: 
    - Docker
    - Yml
---

记录平时用到的 docker-compose.yml 文件集合，目的当然是为了之后方便自己 ctrl c 与 ctrl v 咯。

<!-- more -->

查看 linux 端口占用 

`netstat -ntulp |grep 80 ` //查看80端口使用情况

* -n 拒绝显示别名，能显示数字的全部转化为数字
* -t (tcp) 仅显示tcp相关选项
* -u (udp)仅显示udp相关选项
* -l 仅列出在Listen(监听)的服务状态
* -p 显示建立相关链接的程序名

# 微服务

平台即服务(Paas)
> Gitlab (代码托管平台)
> Nexus （依赖管理平台）
> Registry （docker私有仓库）

# vim 粘贴技巧

在 linux 服务器上，经常会遇到复制代码，粘贴到文件里时，经常会遇到粘贴进来的格式变了

在vim普通模式下执行  `:set paste` 命令，再按 i 插入就会保留原文件格式

如果你想关闭z执行  `:set nopaste`

# GitLab

创建文件 `/usr/local/docker/gitlab/docker-compose.yml` 进入 `/usr/local/docker/gitlab/` 目录，打开 `docker-compose.yml` 复制下面代码并粘贴

``` yml
version: '2'
services:
    gitlab:
      image: 'twang2218/gitlab-ce-zh:11.1.4'
      restart: always
      # hostname 宿主机的IP 或 宿主机的域名指向
      hostname: '192.168.1.27'
      environment:
        TZ: 'Asia/Shanghai'
        GITLAB_OMNIBUS_CONFIG: |
          external_url 'http://192.168.1.27:80'
          gitlab_rails['time_zone'] = 'Asia/Shanghai'
          gitlab_rails['gitlab_shell_ssh_port'] = 2222
          unicorn['port'] = 8888
          nginx['listen_port'] = 80
          # 需要配置到 gitlab.rb 中的配置可以在这里配置，每个配置一行，注意缩进。
          # 比如下面的电子邮件的配置：
          # gitlab_rails['smtp_enable'] = true
          # gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
          # gitlab_rails['smtp_port'] = 465
          # gitlab_rails['smtp_user_name'] = "xxxx@xx.com"
          # gitlab_rails['smtp_password'] = "password"
          # gitlab_rails['smtp_authentication'] = "login"
          # gitlab_rails['smtp_enable_starttls_auto'] = true
          # gitlab_rails['smtp_tls'] = true
          # gitlab_rails['gitlab_email_from'] = 'xxxx@xx.com'
      ports:
        # 左边宿主机端口 ： 右边容器的端口
        - '80:80'
        - '8443:443'
        - '2222:22'
      volumes:
        - /usr/local/docker/gitlab/config:/etc/gitlab
        - /usr/local/docker/gitlab/data:/var/opt/gitlab
        - /usr/local/docker/gitlab/logs:/var/log/gitlab
```

然后使用命令 `docker-compose up -d` 来启动，停止服务使用 `docker-compose down`

## 测试

打开浏览器 ip:80 访问服务

# Nexus

创建文件 `/usr/local/docker/nexus/docker-compose.yml` 进入 `/usr/local/docker/nexus/` 目录，打开 `docker-compose.yml` 复制下面代码并粘贴

``` yml
version: '3.1'
services:
    nexus:
      image: 'sonatype/nexus3'
      restart: unless-stopped
      container_name: nexus
      ports:
        - '8081:8081'
      volumes:
        - /usr/local/docker/nexus/data:/nexus-data
```

然后使用命令 `docker-compose up -d` 来启动，停止服务使用 `docker-compose down`


## 测试

打开浏览器 ip:8081 访问服务
登录，默认 账户：admin  

![nexus sign in](https://s2.ax1x.com/2020/01/16/lv1FfK.png)

### 问题
如果无法访问服务，可以通过 `docker logs 容器ID` 查看原因,具体问题具体分析

```
mkdir: cannot create directory '../sonatype-work/nexus3/log': Permission denied
mkdir: cannot create directory '../sonatype-work/nexus3/tmp': Permission denied
# 说明没有权限
[root@localhost nexus]# docker-compose down
[root@localhost nexus]# chmod 777 data/
[root@localhost nexus]# docker-compose up
```


# Registry

创建文件 `/usr/local/docker/registry/docker-compose.yml` 进入 `/usr/local/docker/registry/` 目录，打开 `docker-compose.yml` 复制下面代码并粘贴

``` yml
version: '3.1'
services:
    registry:
      image: 'registry'
      restart: unless-stopped
      container_name: registry
      ports:
        - '5000:5000'
      volumes:
        - /usr/local/docker/registry/data:/var/lib/registry
```

然后使用命令 `docker-compose up -d` 来启动，停止服务使用 `docker-compose down`

## 测试

打开浏览器 ip:5000/v2/ 访问服务,可以看到一个空对象 `{}`

## 测试镜像上传

我们以 tomcat 为例测试镜像上传功能

```
# 拉取一个镜像
[root@localhost ~]# docker pull tomcat

# 查看全部镜像
[root@localhost ~]# docker images

# 标记本地镜像并指向目标仓库（ip:port/image_name:tag）
[root@localhost ~]# docker tag tomcat 192.168.1.27:5000/tomcat

# 提交镜像到仓库
[root@localhost ~]# docker push 192.168.1.27:5000/tomcat

```

**查看全部镜像**
```
[root@localhost ~]# curl -XGET http://192.168.1.27:5000/v2/_catalog
```

**查看指定镜像**
```
[root@localhost ~]# curl -XGET http://192.168.1.27:5000/v2/tomcat/tags/list
```

## 测试镜像拉取
```
[root@localhost ~]# docker rmi tomcat
[root@localhost ~]# docker rmi 192.168.1.27:5000/tomcat

```

# Docker-registry-frontend

修改文件 `/usr/local/docker/registry/docker-compose.yml` 
进入 `/usr/local/docker/registry/` 目录，
关闭容器 `docker-compose down`
打开 `docker-compose.yml` 复制下面代码并修改为如下

``` yml
version: '3.1'
services:
    registry:
      image: 'registry'
      restart: unless-stopped
      container_name: registry
      ports:
        - '5000:5000'
      volumes:
        - /usr/local/docker/registry/data:/var/lib/registry

    frontend:
      image: 'konradkleine/docker-registry-frontend:v2'
      ports:
        - '8000:80'
      volumes:
        - ./certs/frontend.crt:/etc/apache2/server.crt:ro
        - ./certs/frontend.key:/etc/apache2/server.key:ro
      environment:
        - ENV_DOCKER_REGISTRY_HOST=192.168.1.27
        - ENV_DOCKER_REGISTRY_PORT=5000
```

然后使用命令 `docker-compose up -d` 来启动，停止服务使用 `docker-compose down`


## 测试

打开浏览器 ip:8000 访问服务


# Mysql

创建文件 `/usr/local/docker/mysql/docker-compose.yml` 进入 `/usr/local/docker/mysql/` 目录，打开 `docker-compose.yml` 复制下面代码并粘贴

``` yml
version: '3.1'
services:
    mysql:
      image: 'mysql:5.7.22'
      restart: always
      container_name: mysql
      ports:
        - '3307:3306'
      environment:
        TZ: 'Asia/Shanghai'
        MYSQL_ROOT_PASSWORD: 123456
      command:
        --character-set-server=utf8mb4
        --collation-server=utf8mb4_general_ci
        --explicit_defaults_for_timestamp=true
        --lower_case_table_names=1
        --max_allowed_packet=128M
        --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
      volumes:
        # 在左边宿主机中起了个名字叫 mysql-data 的文件夹，就需要在宿主机定义下 mysql-data 
        - mysql-data:/var/lib/mysql
volumes:
  # 定义 mysql-data ：存放位置(不写一般默认在 /var/lib/docker/volumes)
  mysql-data:
```

然后使用命令 `docker-compose up -d` 来启动，停止服务使用 `docker-compose down`

## 测试
使用本地客户端连接测试  

```
C:\Users\sdqhw>mysql -h192.168.1.27 -P3307 -uroot -p123456
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 265
Server version: 5.7.22 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```


# Gogs

Gogs 一款极易搭建的自助 Git 服务

创建文件 `/usr/local/docker/gogs/docker-compose.yml` 进入 `/usr/local/docker/gogs/` 目录，打开 `docker-compose.yml` 复制下面代码并粘贴

``` yml
version: '2'
services:
  gogs:
    image: gogs/gogs
    container_name: gogs
    restart: always
    volumes:
      - /usr/local/docker/gogs/data:/data
    ports:
      - "3001:3000"
      - "2223:22"
```

然后使用命令 `docker-compose up -d` 来启动，停止服务使用 `docker-compose down`

## 测试与配置

打开浏览器 ip:3001 访问服务,处理访问会进入安装配置页面

![gogs](https://s2.ax1x.com/2020/01/20/1P4oY8.png)
