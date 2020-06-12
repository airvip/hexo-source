---
title: Linux 下进程与端口之间的彼此查看
date: 2020-06-11 16:10:55
tags: 
    - Bash
    - Linux
---

> 我就是一滩烂泥，能不能不要扶我上墙。

工作中经常需要**通过进程占用的端口号**或者**通过端口号查看被哪个进程占用了**

<!-- more -->

## 举例说明

### 通过进程名查看其占用端口

1. 先查看进程的 pid， `ps -aux | grep 进程名`
2. 在通过 pid 查看端口号 `netstat -tlnap | grep 进程pid`

例子：通过 java 进程查看对应的端口号

```
# 查看 java 进程 pid
$ ps -aux | grep java
airvip    5879  0.7  2.4 4923696 403824 ?      Sl   12:49   1:50 java -Xms50m -Xmx128m -jar /home/air-sms-SNAPSHOT.jar
airvip    6097  0.0  0.0 112660   972 pts/1    S+   16:53   0:00 grep --color=auto java
airvip    7389  0.1  2.1 6073516 347652 ?      Sl   Feb10 300:29 java -Xms50m -Xmx128m -jar notice-SNAPSHOT.jar
airvip    7896  0.1  2.0 4496480 329988 ?      Sl    2019 571:22 java -jar -Xms50m -Xmx200m tt-SNAPSHOT.jar


# 通过 java 进程 pid 查看占用端口（进程 pid 为 5879)
$ netstat -tlnap | grep 5879
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 0.0.0.0:34977           0.0.0.0:*               LISTEN      5879/java           
tcp        0      0 127.0.0.1:25879         127.0.0.1:6379          TIME_WAIT   -                   
tcp        0      0 172.17.242.70:80        120.41.35.62:58797      ESTABLISHED -                   
tcp        0      0 127.0.0.1:15879         127.0.0.1:6379          TIME_WAIT   - 
```

可以看到，进程 PID 为 5879 的程序占用着 34977 端口

### 通过端口查看进程

例子：通过端口号 2222 查看进程 

方式一：通过 netstat 方式查看 `netstat -tlnap | grep 端口号`

```
$ netstat -tlnap | grep 2222
tcp        0      0 127.0.0.1:36082         127.0.0.1:2222          TIME_WAIT   -                      
tcp        0      0 127.0.0.1:36076         127.0.0.1:2222          TIME_WAIT   -                   
tcp        0      0 127.0.0.1:36078         127.0.0.1:2222          TIME_WAIT   -                   
tcp6       0      0 :::2222                 :::*                    LISTEN      1373/dockerd
```

方式一：通过 lsof 方式查看 `lsof -i:2222`

```
$ lsof -i:2222
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
dockerd 1373 root   47u  IPv6  47000      0t0  TCP *:EtherNet/IP-1 (LISTEN)
```

**说明** 
提示：Not all processes could be identified, non-owned process info will not be shown, you would have to be root to see it all.
解决：因为是在普通用户下执行的 netstat 命令，所以权限不足，登陆 root 用户，再次执行尝试。