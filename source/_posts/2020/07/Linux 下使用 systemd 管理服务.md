---
title: Linux 下使用 systemd 管理服务
date: 2020-07-28 14:22:06
tags: 
    - Systemctl
    - Linux
---

> 等一等，还你一个更好的明天

SysV init，UpStart，systemd 主要是解决服务引导管理的问题。systemd 对服务的管理（service 类 unit 的管理）主要是依靠 service 类型的 unit 文件进行管控的完成的。

<!-- more -->

SysV init 是最早的解决方案（服务顺序启动，启动过程比较慢）。

1. 原理简单，易于理解；
2. 依靠shell脚本控制，编写服务脚本门槛比较低。

UpStart 主要解决了服务的即插即用。

systemd 的设计理念

1. 尽可能的快速启动服务；
2. 尽可能的减少系统资源占用。

## systemctl 命令

语法：`systemctl [OPTIONS…] COMMAND [SERVICE_NAME.service…]`

1. 启动

使用 `systemctl start SERVICE_NAME.service`  等同于  `service SERVICE_NAME start`


2. 停止

使用 `systemctl stop SERVICE_NAME.service`  等同于  `service SERVICE_NAME stop`

3. 重启

使用 `systemctl restart SERVICE_NAME.service`  等同于  `service SERVICE_NAME restart`

4. 状态

使用 `systemctl status SERVICE_NAME.service`  等同于  `service SERVICE_NAME status`

centos7 里面状态信息里显示的 loaded 表示是否加入到 systemctl 可管理的列表中(类似centos6 中 chkconfig –add 的概念)
loaded 表示已经添加到列表中，如果后面是 disabled 表示没有开机自动启动，如果是 enabled 表示开机自动启动。

5. 条件式重启：(如果服务之前启动了，则对服务进行重启，如果服务之前没启动，则不执行任何操作)

使用 `systemctl tryrestart SERVICE_NAME.service`  等同于  `service SERVICE_NAME condrestart`

6. 重载或重启服务：(如果服务支持不重启便能重载配置文件，就进行重载，如果服务不支持重载配置文件，就重启服务)

`systemctl reload SERVICE_NAME.service` 只重载
`systemctl reload-or-restart SERVICE_NAME.service` 无法重载则重启

7. 重载或条件限制性重启：(如果服务支持不重启便能重载配置文件，就进行重载，如果服务不支持直接重载配置文件，就重启服务,但重启的前提是该服务之前是开启的)

`systemctl reload-or-try-restart SERVICE_NAME.service`

8. 查看某服务当前是否激活

`systemctl is-acive SERVICE_NAME.service`

9. 查看所有已激活的服务

`systemctl list-unit –type|-t service`

10. 查看所有的服务，也就是所有已经装载load的服务，包含已激活和未激活的

使用 `systemctl list-unit -t service -a` 等同于 `chkconfig –list`

显示出来的信息的含义：

loaded:Unit 配置文件已处理
* active(running):一次或多次持续处理的运行
* active(exited):成功完成一次性的配置
* active(waiting):运行中，等待一个事件
* inactive:不运行
* enabled:开机启动
* disabled:开机不启动
* static:开机不启动，但可被另一个启用的服务激活

11. 设置服务可以开机自动启动

使用 `systemctl enable SERVICE_NAME.service` 等同于 `chkconfig SERVICE_NAME on`

12. 禁止服务开机自动启动

使用 `systemctl disable SERVICE_NAME.service` 等同于 `chkconfig SERVICE_NAME off`

13. 查看某服务是否能够开机自启动

使用 `systemctl is-enabled SERVICE_NAME.service` 等同于 `chkconfig –list SERVICE_NAME`

14. 禁止某服务设定为开机自启

`systemctl mask SERVICE_NAME.service`

15. 取消某服务设定为开机自启

`systemctl unmask SERVICE_NAME.service`

16. 查看服务的依赖关系

`systemctl list-dependencies SERVICE_NAME.service`