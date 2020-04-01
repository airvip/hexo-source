---
title: Solr安装及添加中文分词
date: 2020-04-01 14:27:23
tags: 
    - Docker
    - Solr
---

> 都说樱花飘落时让人伤感，而我看到樱花盛开也觉得伤感，因为我会计算，我还能再看几次樱花盛开呢。

Solr 是一个开源独立的企业级搜索应用服务，它建立在 Lucene (全文搜索引擎)之上。Solr 是一个可扩展的，可部署，搜索/存储引擎，优化搜索大量以文本为中心的数据库。

[官方网站](https://jenkins.io/)

<!-- more -->

## docker 编译安装启动

创建文件 `/usr/local/docker/solr/docker-compose.yml` 进入 `/usr/local/docker/solr/` 目录

``` bash
[root@localhost ~]# cd /usr/local/docker/
[root@localhost docker]# mkdir solr
[root@localhost docker]# cd solr
```

打开 `docker-compose.yml` 复制下面代码并粘贴

```
version: '3.1'
services:
  solr:
    #image: solr:8.5
    build: ikanalyzer
    restart: always
    container_name: solr
    ports:
      - 8983:8983
    volumes:
      - ./solrdata:/var/solr/data
```

并新建 solrdata 目录，给与足够的权限

```
[root@localhost solr]# mkdir solrdata
[root@localhost solr]# chmod 777 solrdata
```

创建编译环境目录及相关文件

```
[root@localhost solr]# mkdir ikanalyzer
[root@localhost solr]# cd ikanalyzer
[root@localhost ikanalyzer]# vim Dockerfile
```

Dockerfile 内容如下

```
FROM solr:8.5
MAINTAINER airvip <sdqhwzb@163.com>

# 安装中文分词
WORKDIR /opt/solr/server/solr-webapp/webapp/WEB-INF/lib
ADD ik-analyzer-8.3.0.jar .
WORKDIR /opt/solr/server/solr-webapp/webapp/WEB-INF
ADD IKAnalyzer.cfg.xml .
ADD ext.dic .
ADD stopword.dic .
ADD ik.conf .
ADD dynamicdic.txt .

WORKDIR /opt/solr
```

ik-analyzer-8.3.0.jar 可以在 [sonatype](https://search.maven.org/search?q=com.github.magese) 下载
IKAnalyzer.cfg.xml, ext.dic, stopword.dic, ik.conf, dynamicdic.txt 可以在 [github](https://github.com/magese/ik-analyzer-solr) 下载

直接编译启动

```
[root@localhost ikanalyzer]# cd ..
[root@localhost solr]# docker-compose up -d
[root@localhost solr]# docker exec -it solr /bin/bash
solr@103808bc5e20:/opt/solr-8.5.0$ mkdir -p /var/solr/data/ik_core
solr@103808bc5e20:/opt/solr-8.5.0$ cp -r /opt/solr/server/solr/configsets/sample_techproducts_configs/conf/ /var/solr/data/ik_core
solr@103808bc5e20:/opt/solr-8.5.0$ exit
[root@localhost solr]#
[root@localhost solr]#
[root@localhost solr]# cp ikanalyzer/managed-schema solrdata/ik_core/conf/
cp: overwrite ‘solrdata/ik_core/conf/managed-schema’? yes
[root@localhost solr]#
```

ikanalyzer 目录下的 managed-schema 文件只比原文件多了下面内容

```
<!-- ik分词器 -->
<fieldType name="text_ik" class="solr.TextField">
  <analyzer type="index">
      <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false" conf="ik.conf"/>
      <filter class="solr.LowerCaseFilterFactory"/>
  </analyzer>
  <analyzer type="query">
      <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="true" conf="ik.conf"/>
      <filter class="solr.LowerCaseFilterFactory"/>
  </analyzer>
</fieldType>
```

## 服务配置

浏览器打开 ip:8983,如下图

![solr_index](https://s1.ax1x.com/2020/04/01/G3mcj0.png)

Core Admin 进行配置

![core_admin](https://s1.ax1x.com/2020/04/01/G3nEVS.png)


中文分词测试

![text_ik](https://s1.ax1x.com/2020/04/01/G3uCi4.png)


## 注意

* 如果没有 text_ik 可以重启服务试一下。
* Solr 版本是 8.5  















