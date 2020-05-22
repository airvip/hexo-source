---
title: Linux 安装 Java 环境
date: 2018-10-10 14:06:41
tags: 
    - Java
    - Linux
---

> 漂亮话谁都会说，最重要的是能够去做。

2014 年 3 月 18 日，Oracle 公司发表 Java SE 8。在 Linux 安装 jdk-8u152 的步骤，在此做个笔记，方便以后粘贴复制，^_^  ^_^
 ^_^

<!-- more -->

我的操作系统

```
root@airvip:~# hostnamectl
   Static hostname: airvip
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 27f68799070f446db951db372b1aa27a
           Boot ID: 75dd10e1aa9f42049cdfd8a6cb756e24
    Virtualization: oracle
  Operating System: Ubuntu 18.04.4 LTS
            Kernel: Linux 4.15.0-99-generic
      Architecture: x86-64
```

下载地址 [Java SE Development Kit 8u](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)

## 安装流程

创建目录并进入

```
root@airvip:~# mkdir -p /usr/local/java
root@airvip:~# cd /usr/local/java
```

将下载好的 `jdk-8u152-linux-x64.tar.gz` 文件制动到 `/usr/local/java` 目录解压

```
root@airvip:/usr/local/java# mv ~/jdk-8u152-linux-x64.tar.gz .
root@airvip:/usr/local/java# tar -zxvf jdk-8u152-linux-x64.tar.gz
```

设置所有者为 root

```
root@airvip:/usr/local/java# chown -R root:root /usr/local/java/
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
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
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
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin

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
root@airvip:~# java -version
java version "1.8.0_152"
Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
```

更新其他用户环境变量

```
root@airvip:~# su airvip
root@airvip:~# source /etc/profile
```