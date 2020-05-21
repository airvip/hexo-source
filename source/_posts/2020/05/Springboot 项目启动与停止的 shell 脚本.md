---
title: Springboot 项目启动与停止的 shell 脚本
date: 2020-05-21 18:01:55
tags: 
    - Springboot
    - Shell
---

> 善良没用，你需要漂亮！

现在很多 Springboot 项目都是以 jar 包方式运行，在 Linux 上为了方便我们启动停止服务，于是我们可以通过下面的脚本来快速启动停止服务。

<!-- more -->

## Shell 脚本

```
#!/bin/bash
cd `dirname $0`

CUR_SHELL_DIR=`pwd`
CUR_SHELL_NAME=`basename ${BASH_SOURCE}`

JAR_NAME="jar包的名称"
JAR_PATH=$CUR_SHELL_DIR/$JAR_NAME

#JAVA_MEM_OPTS=" -server -Xms1024m -Xmx1024m -XX:PermSize=128m"
JAVA_MEM_OPTS=""

#如果是多环境配置需要在该选项中指定profile
SPRING_PROFILES_ACTIV="-Dspring.profiles.active="
#如果没有多环境配置将SPRING_PROFILES_ACTIV注释掉，将下面SPRING_PROFILES_ACTIV=""注释放开
#SPRING_PROFILES_ACTIV=""
LOG_DIR=$CUR_SHELL_DIR/logs
LOG_PATH=$LOG_DIR/${JAR_NAME%..log

echo_help()
{
    echo -e "synctax: sh $CUR_SHELL_NAME start|stop"
}

if [ -z $1 ];then
    echo_help
	exit 1
fi

if [ ! -d "$LOG_DIR"];then
    mkdir "$LOG_DIR"
fi

if [ ! -f "$LOG_PATH"];then
    touch "$LOG_DIR"
fi

if [ "$1" == "start" ];then

    # check server
	PIDS=`ps --no-heading -C java -f --width 1000 | grep $JAR_NAME | awk '{print $2}'`
	if [ -n "$PIDS" ]; then
        echo -e "ERROR: The $JAR_NAME already started and the PID is ${PIDS}."
        exit 1
    fi

	echo "Starting the $JAR_NAME..."
	
	# start
	nohup java $JAVA_MEM_OPTS -jar $SPRING_PROFILES_ACTIV $JAR_PATH >> $LOG_PATH 2>&1 &
	
	COUNT=0
    while [ $COUNT -lt 1 ]; do
        sleep 1
        COUNT=`ps  --no-heading -C java -f --width 1000 | grep "$JAR_NAME" | awk '{print $2}' | wc -l`
        if [ $COUNT -gt 0 ]; then
            break
        fi
    done
    PIDS=`ps  --no-heading -C java -f --width 1000 | grep "$JAR_NAME" | awk '{print $2}'`
    echo "${JAR_NAME} Started and the PID is ${PIDS}."
    echo "You can check the log file in ${LOG_PATH} for details."

elif [ "$1" == "stop" ];then
 
    PIDS=`ps --no-heading -C java -f --width 1000 | grep $JAR_NAME | awk '{print $2}'`
    if [ -z "$PIDS" ]; then
        echo "ERROR:The $JAR_NAME does not started!"
        exit 1
    fi
	
	echo -e "Stopping the $JAR_NAME..."
	
	for PID in $PIDS; do
        kill $PID > /dev/null 2>&1
    done
	
	COUNT=0
    while [ $COUNT -lt 1 ]; do
        sleep 1
        COUNT=1
        for PID in $PIDS ; do
            PID_EXIST=`ps --no-heading -p $PID`
            if [ -n "$PID_EXIST" ]; then
                COUNT=0
                break
            fi
        done
    done
 
    echo -e "${JAR_NAME} Stopped and the PID is ${PIDS}."
else
    echo_help
    exit 1
fi
```

## 使用方式

1. 将上面的 Shell 脚本复制下来保存为 xxx.sh
2. 将 自己的 xxx.jar 包和 xxx.sh 文件上传到需要部署的 Linux 相关目录中
3. 给 xx.sh 分配权限 ： chmod 777 xxx.sh
4. 启动： ./xxx.sh start
5. 停止： ./xxx.sh stop





