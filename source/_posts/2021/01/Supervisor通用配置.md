---
title: Supervisor通用配置
date: 2021-01-13 15:45:51
tags: 
	- Supervisor
---

> 一直等一等，会一直等一等

Supervisor 是一个 Python 开发的通用的进程管理程序,可以管理和监控 Linux 上面的进程,能将一个普通的命令行进程变为后台 daemon，并监控进程状态，异常退出时能自动重启。

<!-- more -->

Supervisor 通过 fork/exec 的方式把这些被管理的进程当作 supervisor 的子进程来启动，这样只要在 supervisor 的配置文件中，把要管理的进程的可执行文件的路径写进去即可。

Supervisor 可以实现当子进程挂掉的时候，父进程可以准确获取子进程挂掉的信息的，可以选择是否自己启动和报警。

Supervisor 提供了一个功能，可以为 supervisord 或者每个子进程，设置一个非 root 的 user，这个 user 就可以管理它对应的进程。

## 安装 supervisor

Debian/Ubuntu 可通过 apt 安装(推荐)

```
root@airvip:~# apt -y install supervisor
```

pip 安装
```
root@airvip:~# pip install supervisor
```
### apt 安装后无需配置

apt 安装后相对应的配置文件已经有了，并且已经启动还加入了开机自启
```
root@airvip:~# cd /etc/supervisor/
root@airvip:/etc/supervisor# ls
conf.d  supervisord.conf
root@airvip:/etc/supervisor#
root@airvip:/etc/supervisor# systemctl status supervisor
● supervisor.service - Supervisor process control system for UNIX
   Loaded: loaded (/lib/systemd/system/supervisor.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2021-01-12 07:59:42 UTC; 24h ago
     Docs: http://supervisord.org
   ...
root@airvip:/etc/supervisor#
```

### pip 安装后配置

pip 安装后需要手动配置下
```
root@airvip:~# echo_supervisord_conf > supervisord.conf
root@airvip:~# cd /etc & mkdir supervisor
root@airvip:~# mv ~/supervisord.conf /etc/supervisor/
root@airvip:/etc/supervisor# vim supervisord.conf

最后改成
[include]
files = /etc/supervisor/conf.d/*.conf

root@airvip:/etc/supervisor# mkdir conf.d
```

启动
```
root@airvip:/etc/supervisor# supervisord -c /etc/supervisor/supervisord.conf
```
查看是否启动
```
root@airvip:/etc/supervisor# ps -aux | grep supervisor
root      2675  0.0  0.0  13136  1104 pts/2    S+   09:22   0:00 grep --color=auto supervisor
root     27948  0.0  1.0  66252 22108 ?        Ss   Jan12   0:21 /usr/bin/python /usr/bin/supervisord -c /etc/supervisor/supervisord.conf
```

## supervisorctl

supervisor 已经启动，我们可以利用 supervisorctl 来管理
```
root@airvip:/etc/supervisor/conf.d# supervisorctl
supervisor> 
```
## supervisorctl 常用命令
```
supervisor> status  # 查看程序状态
supervisor> start flask-im   # 启动 flask-im 单一程序
supervisor> stop flask-demo:*  # 关闭 flask-demo 组程序
supervisor> start flask-demo:*  # 启动 flask-demo 组程序
supervisor> restart flask-demo:*  # 重新启动 flask-demo 组程序
supervisor> update  # 重启配置文件修改过的程序
supervisor> reload  // 重新启动配置文件中的所有程序
supervisor> exit   # 退出 
```

supervisor 项目示例 `/etc/supervisor/conf.d/flask-app.conf`
```
[group:flask_app]
programs=flask-demo-app


[program:flask-demo-app]
directory=/root/python_app/flask-demo
command=/root/python_app/flask-demo/scripts/manage.sh
autostart=true      
startsecs=10        
autorestart=true         
startretries=3           
user=root         
priority=999           
redirect_stderr=true  
stdout_logfile_maxbytes=50MB  
stdout_logfile_backups=10   
stdout_logfile=/mnt/logs/xx_stdout.log
stderr_logfile=/mnt/logs/xx_stderr.log 
loglevel=info
stopsignal=KILL
stopasgroup=true      
killasgroup=true
```

### 配置文件详解

- command：启动程序使用的命令，可以是绝对路径或者相对路径
- process_name：一个python字符串表达式，用来表示supervisor进程启动的这个的名称，默认值是%(program_name)s
- numprocs：Supervisor启动这个程序的多个实例，如果numprocs>1，则process_name的表达式必须包含%(process_num)s，默认是1
- numprocs_start：一个int偏移值，当启动实例的时候用来计算numprocs的值
- priority：权重，可以控制程序启动和关闭时的顺序，权重越低：越早启动，越晚关闭。默认值是999
- autostart：如果设置为true，当supervisord启动的时候，进程会自动重启。
- autorestart：值可以是false、true、unexpected。false：进程不会自动重启，unexpected：当程序退出时的退出码不是exitcodes中定义的时，进程会重启，true：进程会无条件重启当退出的时候。
- startsecs：程序启动后等待多长时间后才认为程序启动成功
- startretries：supervisord尝试启动一个程序时尝试的次数。默认是3
- exitcodes：一个预期的退出返回码，默认是0,2。
- stopsignal：当收到stop请求的时候，发送信号给程序，默认是TERM信号，也可以是 HUP, INT, QUIT, KILL, USR1, or USR2。
- stopwaitsecs：在操作系统给supervisord发送SIGCHILD信号时等待的时间
- stopasgroup：如果设置为true，则会使supervisor发送停止信号到整个进程组
- killasgroup：如果设置为true，则在给程序发送SIGKILL信号的时候，会发送到整个进程组，它的子进程也会受到影响。
- user：如果supervisord以root运行，则会使用这个设置用户启动子程序
- redirect_stderr：如果设置为true，进程则会把标准错误输出到supervisord后台的标准输出文件描述符。
- stdout_logfile：把进程的标准输出写入文件中，如果stdout_logfile没有设置或者设置为AUTO，则supervisor会自动选择一个文件位置。
- stdout_logfile_maxbytes：标准输出log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小
- stdout_logfile_backups：标准输出日志轮转备份的数量，默认是10，如果设置为0，则不备份
- stdout_capture_maxbytes：当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB
- stdout_events_enabled：如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发
- stderr_logfile：把进程的错误日志输出一个文件中，除非redirect_stderr参数被设置为true
- stderr_logfile_maxbytes：错误log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小
- stderr_logfile_backups：错误日志轮转备份的数量，默认是10，如果设置为0，则不备份
- stderr_capture_maxbytes：当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB
- stderr_events_enabled：如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发
- environment：一个k/v对的list列表
- directory：supervisord在生成子进程的时候会切换到该目录
- umask：设置进程的umask
- serverurl：是否允许子进程和内部的HTTP服务通讯，如果设置为AUTO，supervisor会自动的构造一个url