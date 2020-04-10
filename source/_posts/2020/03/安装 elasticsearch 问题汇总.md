---
title: 安装 elasticsearch 问题汇总
date: 2020-03-03 09:51:11
tags: 
    - Elasticsearch
    - Docker
---

> 只要世间有酒,我就喝不够!

Elasticsearch 是一个基于 Lucene 的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于 RESTful web 接口。Elasticsearch 是用 Java 语言开发的，并作为 Apache 许可条款下的开放源码发布，是一种流行的企业级搜索引擎。

[官方网站](https://www.elastic.co/cn/elasticsearch)

<!-- more -->

## docker 安装 elasticsearch

docker-compose.yml 如下

```
version: '3.3'
services:
  elasticsearch:
    image: elasticsearch:7.6.2
    container_name: elasticsearch
    restart: 'no'
    ports:
      - 9200:9200
      - 9300:9300
    environment:
      discovery.type: single-node
```

安装启动

``` bash
$ docker-compose up -d
$ docker logs -f elasticsearch
```

## 常见错误信息

问题： max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

解决：

临时处理，重启机器将失效
```
# 切换到 root
$ su -
# 设置内存
$ sysctl -w vm.max_map_count=262144
# 查看设置
$ sysctl -a|grep vm.max_map_count
```

永久处理

```
$ vim /etc/sysctl.conf

# 在文件最后添加如下内容
vm.max_map_count=262144
```

----

问题： max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]

解决：

```
# 切换到 root
$ su -
$ vim /etc/security/limits.conf

# 在文件最后添加如下内容
*** hard nofile 65536
*** soft nofile 65536

$ source /etc/security/limits.conf
```

