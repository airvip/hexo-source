---
title: Spring Cloud Alibaba系列-消息队列 RocketMQ 安装
date: 2020-04-10 16:34:22
tags: 
    - Spring Cloud Gateway
    - Java
    - 微服务
    - RocketMQ
    - Docker
---

> 大部分人并不想长大，只是没办法继续当一个小孩子

消息队列中间件是分布式系统中重要的组件，主要解决应用耦合、异步消息、流量削锋等问题。实现高性能、高可用、可伸缩和最终一致性架构。是大型分布式系统不可缺少的中间件。

<!-- more -->

系统解耦： 解决不同重要程度、不同能力级别系统之间依赖导致一死全死
异步消息： 当存在一对多调用时，可以发一条消息给消息系统，让消息系统通知相关系统
流量削锋： 主要解决瞬时写压力大于应用服务能力导致消息丢失、系统奔溃等问题
蓄流压测： 线上有些链路不好压测，可以通过堆积一定量消息再放开来压测

## [What] Rocketmq 是什么

RocketMQ 是阿里开源的消息中间件，它是纯 Java 开发，具有高吞吐量、高可用性、适合大规模分布式系统应用的特点。RocketMQ 思路起源于 Kafka，但并不是 Kafka 的一个 Copy，它对消息的可靠传输及事务性做了优化，目前在阿里集团被广泛应用于交易、充值、流计算、消息推送、日志流式处理、binglog 分发等场景。
RocketMQ 消息中间件中有两个角色：消息生产者和消息消费者。消息生产者负责创建消息并发送到 RocketMQ 服务器，RocketMQ 服务器会将消息持久化到磁盘，消息消费者从 RocketMQ 服务器拉取消息并提交给应用消费。


RocketMQ 是一款分布式、队列模型的消息中间件，具有以下特点：

* 支持严格的消息顺序
* 支持 Topic 与 Queue 两种模式
* 亿级消息堆积能力
* 比较友好的分布式特性
* 同时支持 Push 与 Pull 方式消费消息


## [Why] 为什么使用 Rocketmq

目前主流的 MQ 主要是 kafka、RocketMQ，Rocketmq 相比于 RocketMQ、kafka 具有主要优势特性有：

* 支持事务型消息（消息发送和DB操作保持两方的最终一致性，RocketMQ 和 kafka 不支持）
* 支持结合rocketmq的多个系统之间数据最终一致性（多方事务，二方事务是前提）
* 支持18个级别的延迟消息（ RocketMQ 和 kafka 不支持）
* 支持指定次数和时间间隔的失败消息重发（ kafka 不支持，RocketMQ 需要手动确认）
* 支持consumer端tag过滤，减少不必要的网络传输（ RocketMQ 和 kafka 不支持）
* 支持重复消费（RocketMQ 不支持，kafka 支持）



## [How] 如何安装 RocketMQ

**一键部署** 

使用 docker-compose, 版本 4.7.0

```
git clone  https://github.com/foxiswho/docker-rocketmq.git

cd docker-rocketmq

cd rmq

chmod +x  start.sh

./start.sh
```

访问浏览器 `localhost:8180`

注意 如果你的微服务或者项目在开发的时候没有放入 docker 中或者与 rocketmq 容器不能直接用 IP 访问， 那么请把 `broker.conf` 中的 `#brokerIP1=192.168.0.253` 前面#号去掉，并且把后面的IP地址改成你的 rocketmq 容器宿主机 IP 地址, 否则报 `com.alibaba.rocketmq.remoting.exception.RemotingConnectException: connect to <172.0.0.120:10909> failed` 配置文件 在 `rmq/rmq/brokerconf` 目录下。

**手动创建安装**

```
root@ubuntu:~# mkdir -p /usr/local/docker/rocketmq
root@ubuntu:~# cd /usr/local/docker/rocketmq

# 创建目录
root@ubuntu:/usr/local/docker/rocketmq# mkdir -p ./rmqs/logs
root@ubuntu:/usr/local/docker/rocketmq# mkdir -p ./rmqs/store
root@ubuntu:/usr/local/docker/rocketmq# mkdir -p ./rmq/logs
root@ubuntu:/usr/local/docker/rocketmq# mkdir -p ./rmq/store

# 设置目录权限
root@ubuntu:/usr/local/docker/rocketmq# chmod -R 777 ./rmqs/logs
root@ubuntu:/usr/local/docker/rocketmq# chmod -R 777 ./rmqs/store
root@ubuntu:/usr/local/docker/rocketmq# chmod -R 777 ./rmq/logs
root@ubuntu:/usr/local/docker/rocketmq# chmod -R 777 ./rmq/store

# 创建配置
root@ubuntu:/usr/local/docker/rocketmq# mkdir -p ./rmq/brokerconf
root@ubuntu:/usr/local/docker/rocketmq# cd rmq/brokerconf
root@ubuntu:/usr/local/docker/rocketmq/rmq/brokerconf# vim broker.conf
（复制下面 broker.conf 文件内容进行粘贴）

root@ubuntu:/usr/local/docker/rocketmq/rmq/brokerconf# cd ../../
root@ubuntu:/usr/local/docker/rocketmq# vim docker-compose.yml
（复制下面 docker-compose.yml 文件内容进行粘贴）

root@ubuntu:/usr/local/docker/rocketmq# docker-compose up -d
```

`broker.conf` 文件内容如下

```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.


#所属集群名字
brokerClusterName=DefaultCluster

#broker名字，注意此处不同的配置文件填写的不一样，如果在broker-a.properties使用:broker-a,
#在broker-b.properties使用:broker-b
brokerName=broker-a

#0 表示Master，>0 表示Slave
brokerId=0

#nameServer地址，分号分割
#namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876

#启动IP,如果 docker 报 com.alibaba.rocketmq.remoting.exception.RemotingConnectException: connect to <192.168.0.120:10909> failed
# 解决方式1 加上一句producer.setVipChannelEnabled(false);，解决方式2 brokerIP1 设置宿主机IP，不要使用docker 内部IP
#brokerIP1=192.168.0.253

#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4

#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭 ！！！这里仔细看是false，false，false
#原因下篇博客见~ 哈哈哈哈
autoCreateTopicEnable=true

#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true

#Broker 对外服务的监听端口
listenPort=10911

#删除文件时间点，默认凌晨4点
deleteWhen=04

#文件保留时间，默认48小时
fileReservedTime=120

#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824

#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000

#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
#storePathRootDir=/home/ztztdata/rocketmq-all-4.1.0-incubating/store
#commitLog 存储路径
#storePathCommitLog=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/commitlog
#消费队列存储
#storePathConsumeQueue=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/consumequeue
#消息索引存储路径
#storePathIndex=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/index
#checkpoint 文件存储路径
#storeCheckpoint=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/checkpoint
#abort 文件存储路径
#abortFile=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/abort
#限制的消息大小
maxMessageSize=65536

#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000

#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=ASYNC_MASTER

#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH

#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```


`docker-compose.yml` 文件内容如下

```
version: '3.5'

services:
  rmqnamesrv:
    image: foxiswho/rocketmq:4.7.0
    container_name: rmqnamesrv
    ports:
      - 9876:9876
    volumes:
      - ./rmqs/logs:/opt/logs
      - ./rmqs/store:/opt/store
    environment:
      JAVA_OPT_EXT: "-Duser.home=/opt -Xms512M -Xmx512M -Xmn128m"
    command: ["sh","mqnamesrv"]
    networks:
        rmq:
          aliases:
            - rmqnamesrv
  rmqbroker:
    image: foxiswho/rocketmq:4.7.0
    container_name: rmqbroker
    ports:
      - 10909:10909
      - 10911:10911
    volumes:
      - ./rmq/logs:/opt/logs
      - ./rmq/store:/opt/store
      - ./rmq/brokerconf/broker.conf:/etc/rocketmq/broker.conf
    environment:
        JAVA_OPT_EXT: "-Duser.home=/opt -Xms512M -Xmx512M -Xmn128m"
    command: ["sh","mqbroker","-c","/etc/rocketmq/broker.conf","-n","rmqnamesrv:9876","autoCreateTopicEnable=true"]
    depends_on:
      - rmqnamesrv
    networks:
      rmq:
        aliases:
          - rmqbroker

  rmqconsole:
    image: styletang/rocketmq-console-ng
    container_name: rmqconsole
    ports:
      - 8180:8080
    environment:
        JAVA_OPTS: "-Drocketmq.namesrv.addr=rmqnamesrv:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false"
    depends_on:
      - rmqnamesrv
    networks:
      rmq:
        aliases:
          - rmqconsole

networks:
  rmq:
    name: rmq
    driver: bridge
```

打开浏览器访问 `IP:8180`

![rocketmq_console](https://s1.ax1x.com/2020/04/10/GTwhdO.png)


