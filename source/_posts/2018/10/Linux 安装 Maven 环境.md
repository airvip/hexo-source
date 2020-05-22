---
title: Linux 安装 Java 环境
date: 2018-10-10 14:43:22
tags: 
    - Java
    - Linux
    - Maven
---

> 漂亮话谁都会说，最重要的是能够去做。

Maven， 是基于项目对象模型（Project Object Model， POM），通过一小段描述信息来管理项目的构建，报告，文档的软件项目管理工具。
Maven 一般在开发环境安装配置即可。

<!-- more -->

下载地址 [apache-maven-3.5.4-bin](https://archive.apache.org/dist/maven/maven-3/3.5.4/binaries/)

[最新版下载地址](https://maven.apache.org/download.cgi)

## 安装流程

创建目录并进入

```
root@airvip:~# mkdir -p /usr/local/maven
root@airvip:~# cd /usr/local/maven
```

将下载好的 `jdk-8u152-linux-x64.tar.gz` 文件制动到 `/usr/local/java` 目录解压

```
root@airvip:/usr/local/maven# wget https://archive.apache.org/dist/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz
root@airvip:/usr/local/maven# tar -zxvf apache-maven-3.5.4-bin.tar.gz
```

## 配置环境变量

配置系统环境变量

```
root@airvip:~# vim /etc/environment
```

修改为如下内容

```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"

export JAVA_HOME=/usr/local/java/jdk1.8.0_152
export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre
export MAVEN_HOME=/usr/local/maven/apache-maven-3.5.4
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$MAVEN_HOME/bin
```

配置用户环境变量


```
root@airvip:~# vim /etc/profile
```

修改为如下内容

```
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "${PS1-}" ]; then
  if [ "${BASH-}" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

export JAVA_HOME=/usr/local/java/jdk1.8.0_152
export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre
export MAVEN_HOME=/usr/local/maven/apache-maven-3.5.4
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin:$MAVEN_HOME/bin

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
```

使用户环境变量生效

```
root@airvip:~# source /etc/profile
```

测试是否安装成功

```
root@airvip:~# mvn -version
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-17T18:33:14Z)
Maven home: /usr/local/maven/apache-maven-3.5.4
Java version: 1.8.0_152, vendor: Oracle Corporation, runtime: /usr/local/java/jdk1.8.0_152/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-99-generic", arch: "amd64", family: "unix"
```

更新其他用户环境变量

```
root@airvip:~# su airvip
root@airvip:~# source /etc/profile
```

配置镜像仓库

```
root@airvip:~# vim /usr/local/maven/apache-maven-3.5.4/conf/settings.xml
```

找到 <mirrors> </mirrors> 节点，将下面的内容复制到 <mirrors> </mirrors> 节点中
```
  <!-- 阿里云仓库 -->
	<mirror>
	    <id>alimaven</id>
	    <mirrorOf>central</mirrorOf>
	    <name>aliyun maven</name>
	    <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
	</mirror>
	    
	<!-- 中央仓库1 -->
	<mirror>
	    <id>repo1</id>
	    <mirrorOf>central</mirrorOf>
	    <name>Human Readable Name for this Mirror.</name>
	    <url>http://repo1.maven.org/maven2/</url>
	</mirror>
	   
	<!-- 中央仓库2 -->
	<mirror>
	    <id>repo2</id>
	    <mirrorOf>central</mirrorOf>
	    <name>Human Readable Name for this Mirror.</name>
	    <url>http://repo2.maven.org/maven2/</url>
	</mirror>
```

配置本地仓库

找到 <localRepository> </localRepository> 节点(默认被注释掉的)，修改为如下内容

```
<localRepository>/usr/local/maven/apache-maven-3.5.4/repo</localRepository>
```
