---
title: Spring Cloud Alibaba系列-链路追踪 SkyWalking
date: 2020-04-09 10:03:00
tags: 
    - SkyWalking
    - Java
    - 微服务
    - Elasticsearch
---

> 曾经沧海难为水，除却巫山不是云。

链路追踪，就是指一次请求任务的开始到结束，期间调用的所有系统 (服务) 及耗时（时间跨度）都可以完整记录下来。通过链路追踪我们可以非常方便的分析各个系统 (服务) 的性能，，以便发生故障的时候，能够快速定位和解决问题，这就是所谓的 APM（应用性能管理）。

<!-- more -->

## [What] Apache SkyWalking 是什么

目前主要的一些 APM 工具有: Cat、Zipkin、Pinpoint、SkyWalking。

Apache SkyWalking 已成为 Apache 基金会顶级项目。
Apache SkyWalking 完全由国人主导开发，在国内社区相当活跃。
Apache SkyWalking 是分布式系统的应用程序性能监视工具，专为微服务、云原生架构和基于容器（Docker、K8s、Mesos）架构而设计。
Apache SkyWalking 是观察性分析平台和应用性能管理系统。提供分布式追踪、服务网格遥测分析、度量聚合和可视化一体化解决方案。支持Java, .Net Core, PHP, NodeJS, Golang, LUA语言探针，支持Envoy + Istio构建的Service Mesh。

![SkyWalking_struct](https://s1.ax1x.com/2020/04/09/G5VxTH.png)


* Skywalking Agent： 使用 JavaAgent 做字节码植入，无侵入式的收集，并通过 HTTP 或者 gRPC 方式发送数据到 SkyWalking Collector。
* SkyWalking Collector： 链路数据收集器，对 agent 传过来的数据进行整合分析处理并落入相关的数据存储中。
* Storage： SkyWalking 的存储，时间更迭，2020 年 3 月 22 日 SkyWalking APM 7.0.0 发布, 探针升级到 JDK1.8，支持代码级性能剖析
* UI： Web 可视化平台，用来展示落地的数据。

### Apache SkyWalking 功能特性

* 多种监控手段，语言探针和服务网格(Service Mesh)
* 多语言自动探针，Java，.NET Core 和 Node.JS
* 轻量高效，不需要大数据
* 模块化，UI、存储、集群管理多种机制可选
* 支持告警
* 优秀的可视化方案


## [Why] 为什么使用 Apache SkyWalking

微服务极大地改变了软件的开发和交付模式，单体应用被拆分为多个微服务，单个服务的复杂度大幅降低，库之间的依赖也转变为服务之间的依赖。由此带来的问题是部署的粒度变得越来越细，众多服务给运维带来巨大压力。客户端使用 REST 调用一个接口，可能需要多个服务协同才能完成这个功能，如果链路上任何一个服务出现问题或者网络超时，都会形成导致接口调用失败。随着业务的不断扩张，服务之间互相调用会越来越复杂。

因此，我们就需要些可以帮助理解系统行为、用于分析性能问题的工具。基于 Apache SkyWalking 种种特性 (最主要是国人开发)，所以他成了我们的不二之选。


## [How] 如何使用 Apache SkyWalking


Apache SkyWalking 分为服务端与客户端两部分。

### 服务端配置

SkyWalking 存储方案有多种，官方推荐的方案是 ElasticSearch ，并且最新的 SkyWalking APM 7 也已经支持 ElasticSearch 7，所以我们需要安装最版本 ElasticSearch。


**安装 elasticsearch**

我们通过 docker-compose 方式安装。

docker-compose.yml 内容如下：

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
      cluster.name: elasticsearch
      discovery.type: single-node
```

其中，`9200` 端口号为 SkyWalking 配置 ElasticSearch 所需端口号，`cluster.name `为 SkyWalking 配置 ElasticSearch 集群的名称

浏览器访问 `http://IP:9200/`,浏览器返回信息如下

![elasticsearch_9200](https://s1.ax1x.com/2020/04/09/G5Zuhn.png)

**下载 SkyWalking** 

官方已经为我们准备好了编译过的服务端版本，[下载地址](http://skywalking.apache.org/downloads/)

![skywalking_apm](https://s1.ax1x.com/2020/04/09/G5VTYR.png)

**配置**

下载完成后解压缩，进入 `apache-skywalking-apm-bin-es7/config` 目录并修改 `application.yml` 配置文件

![skywalking_application](https://s1.ax1x.com/2020/04/09/G5ZX3q.png)

* 修改 ElasticSearch7 存储方案
* 修改 ElasticSearch7 服务器地址

**启动 SkyWalking**

修改完配置后，进入 `apache-skywalking-apm-bin-es7/bin` 目录，运行 `startup.bat` 启动服务端

![skywalking_startup](https://s1.ax1x.com/2020/04/09/G5mleU.png)

浏览器访问 `http://localhost:8080/`,浏览器返回信息如下

![skywalking_index](https://s1.ax1x.com/2020/04/09/G5Z24A.png)

### SkyWalking 客户端配置

我们需要使用官方提供的 Java Agent 服务器探针来达到监控的目的。

服务器探针文件在 `apache-skywalking-apm-bin-es7/agent` 目录下

**IDEA 部署探针**

创建一个名为 `spring-cloud-external-skywalking` 的目录，并将 agent 整个目录拷贝进来,如下

![skywalking_agent](https://s1.ax1x.com/2020/04/09/G5MPpj.png)

修改项目的 VM 运行参数，点击 IDEA 菜单栏中的 `Run` > `EditConfigurations...`，我们以 nacos-provider 项目为例，修改参数如下

```
-javaagent:D:\workspace\spring-cloud-alibaba\spring-cloud-external-skywalking\agent\skywalking-agent.jar
-Dskywalking.agent.service_name=nacos-provider
-Dskywalking.collector.backend_service=localhost:11800
```

![idea_vm](https://s1.ax1x.com/2020/04/09/G5Mw3d.png)

* -javaagent：用于指定探针路径
* -Dskywalking.agent.service_name：用于重写 `agent/config/agent.config` 配置文件中的服务名
* -Dskywalking.collector.backend_service：用于重写 `agent/config/agent.config` 配置文件中的服务地址

**命令行直接启动应用，通过 Java Agent 方式启动探针**

```
java -javaagent:/探针路径/skywalking-agent.jar -Dskywalking.agent.service_name=nacos-provider -Dskywalking.collector.backend_service=localhost:11800 -jar spring-cloud-alibaba-nacos-provider-1.0.0-SNAPSHOT.jar
```

把所有服务都添加好 Agent 并启动，就可以通过浏览器访问 `http://localhost:8080/` 对我们服务进行查看分析

![skywalking_topology](https://s1.ax1x.com/2020/04/09/G5UOjU.png)


## 将扩展项目打包 Maven Assembly

其实我们 `spring-cloud-external-skywalking` 项目属于使用了第三方的扩展项目，我们可以通过 `Maven Assembly` 插件来打包。

Assembly 插件目的是提供一个把工程依赖元素、模块、网站文档等其他文件存放到单个归档文件里。

Assembly 可打包的归档文件类型 zip、 tar.gz 、tar.bz2、jar、dir、war。

我们通过 Assembly 将 `spring-cloud-external-skywalking` 打包为 `tar.gz` 文件。

在 spring-cloud-external-skywalking 文件加下新建 pom.xml 文件。

### POM

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>wang.diff</groupId>
    <artifactId>spring-cloud-external-skywalking</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-cloud-external-skywalking</name>
    <description>Learn Spring Cloud Alibaba Start</description>
    <url>https://diff.wang</url>
    <inceptionYear>2020-Now</inceptionYear>


    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <executions>
                    <!-- 配置执行器 -->
                    <execution>
                        <id>make-assembly</id>
                        <!-- 绑定到 package 生命周期阶段上 -->
                        <phase>package</phase>
                        <goals>
                            <!-- 只运行一次 -->
                            <goal>single</goal>
                        </goals>
                        <configuration>
                            <finalName>skywalking-agent</finalName>
                            <descriptors>
                                <!-- 配置描述文件路径 -->
                                <descriptor>src/main/resources/assembly.xml</descriptor>
                            </descriptors>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
 
配置描述为文件 `src/main/resources/assembly.xml`, 所以创建 `src/main/resources/assembly.xml` 配置文件 

```
<assembly>
    <id>7.0.0</id>
    <formats>
        <!-- 打包的文件格式，支持 zip、tar.gz、tar.bz2、jar、dir、war -->
        <format>tar.gz</format>
    </formats>
    <!-- tar.gz 压缩包下是否生成和项目名相同的根目录，有需要请设置成 true -->
    <includeBaseDirectory>false</includeBaseDirectory>
    <dependencySets>
        <dependencySet>
            <!-- 是否把本项目添加到依赖文件夹下，有需要请设置成 true -->
            <useProjectArtifact>false</useProjectArtifact>
            <outputDirectory>lib</outputDirectory>
            <!-- 将 scope 为 runtime 的依赖包打包 -->
            <scope>runtime</scope>
        </dependencySet>
    </dependencySets>
    <fileSets>
        <fileSet>
            <!-- 设置需要打包的文件路径 -->
            <directory>agent</directory>
            <!-- 打包后的输出路径 -->
            <outputDirectory></outputDirectory>
        </fileSet>
    </fileSets>
</assembly>
```

**打包**

```
# 在 target 目录下创建名为 spring-cloud-external-skywalking-1.0.0-SNAPSHOT-7.0.0.tar.gz 的压缩包
mvn clean package
# 在本地仓库目录下创建名为 spring-cloud-external-skywalking-1.0.0-SNAPSHOT-7.0.0.tar.gz 的压缩包
mvn clean install
# 部署到私服，需要再 pom.xml 中添加 distributionManagement 来指定 Maven 分发构件的位置
mvn clean deploy
```

我的如下

```
    <distributionManagement>
        <repository>
            <id>releases</id>
            <name>Nexus Release Repository</name>
            <url>http://192.168.226.136:8081/nexus/content/repositories/releases/</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://192.168.226.136:8081/nexus/content/repositories/snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```
Maven 区别对待 release 版本的构件和 snapshot 版本的构件，
snapshot 为开发过程中的版本，实时，但不稳定，
release版本则比较稳定。
Maven 会根据你项目的版本来判断将构件分发到哪个仓库。