---
title: Windows下Maven安装预配置
date: 2019-12-31 10:54:11
tags: 
    - Maven
    - Java
---

> 有道无术，术尚可求，有术无道，止于术。

Maven 是一个基于 Java 的工具，所以安装 Maven 的前提是你已经安装了 Java JDK。如果你还未安装 JDK，请先优先安装 JDK。

<!-- more  -->

# 安装
学习第一步，安装拦路虎。目前最新版 Apache Maven 为 3.6.3，我们可以去官网下载 [官网下载点这里](http://maven.apache.org/download.cgi) 

系统要求

| 设备  |  需求 | 
|---|---|
|Java Development Kit (JDK)	| Maven3.3+需要JDK1.7或更高版本才能执行-它们仍然允许您使用工具链针对1.3和其他JDK版本进行构建。 |
|内存 |	没有最低要求。(目前最少也是8G了吧) |
|硬盘	| 对于 Maven 安装本身来说，需要大约为 10MB。除此之外，额外的磁盘空间将用于本地Maven存储库。本地存储库的大小将根据使用情况而有所不同，但至少需要500MB。 |
|操作系统 |	没有最低要求。启动脚本包含在shell脚本和Windows批处理文件中。 |

下载

![apache-maven-3.6.3](http://note.youdao.com/yws/res/11142/ECC63FCBFA194EDD90E7395757E6C623)


下载下来之后，直接解压缩到 D 盘（建议直接放在 D 盘，当然你放在哪都无所谓，你要你喜欢）

# 配置

添加环境变量 MAVEN_HOME

操作步骤 按下 `windows`+`R` 键 -> 调出运行输入 `control` -> 点击[系统]选项 -> 在面板左侧点击 [高级系统设置] -> 在出现的系统属性选项卡中选择[高级] -> 点击[环境变量]

新建系统变量 MAVEN_HOME，变量值：D:\apache-maven-3.6.3

![system-var](https://note.youdao.com/yws/res/11147/WEBRESOURCEf954cfaebc58cb552e21b7da801bc261)

编辑系统变量 Path，添加变量值：`%MAVEN_HOME%\bin;` 

![edit-path](https://note.youdao.com/yws/res/11149/WEBRESOURCE7c7a8d3af561c53de46184d395d3c6b3)

注意：注意多个值之间需要有分号隔开，然后点击确定。


# 检验

按下 `windows`+`R` 键 -> 调出运行输入 `cmd`

在cmd窗口中输入 `mvn -version` 检测经过如下：

```
Microsoft Windows [版本 10.0.15063]
(c) 2017 Microsoft Corporation。保留所有权利。
活动代码页: 936

C:\Users\sdqhw>mvn -version
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: D:\apache-maven-3.6.3\bin\..
Java version: 11.0.2, vendor: Oracle Corporation, runtime: D:\Program Files\Java\jdk-11.0.2
Default locale: zh_CN, platform encoding: UTF-8
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
活动代码页: 936

C:\Users\sdqhw>
```





