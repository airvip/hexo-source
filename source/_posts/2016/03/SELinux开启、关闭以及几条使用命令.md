---
title: SELinux开启、关闭以及几条使用命令
date: 2016-03-07 15:13:17
tags:
    - Selinux
---

> 天空是连着的，如果我们也能各自发光的话，无论距离有多远，都能看到彼此努力的身影。

SELinux 给人最直观的感受就是“安全的linux”，确实如此。SELinux 是功能最全面，而且测试最充分的，它是在 20 年的 MAC 研究基础上建立的。

<!-- more -->

SELinux是「Security-Enhanced Linux」的简称，是美国国家安全局「NSA=The National Security Agency」 和SCC(Secure Computing Corporation)开发的 Linux的一个扩张强制访问控制安全模块。原先是在Fluke上开发的，2000年以 GNU GPL 发布。

相关链接 [更详细的selinux介绍（全英文）](http://selinuxproject.org/page/Main_Page)

# SELinux 基本操作

查看SElinux 的状态
``` bash
[root@localhost ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28
```

查看selinux的模式
``` bash
[root@localhost ~]# getenforce
Enforcing
```

设置selinux模式（不需要reboot）
``` bash
[root@localhost ~]# setenforce 0
```

SELINUX有「disabled」「permissive」，「enforcing」3种选择。
  1. disabled当然就是禁用关闭了。
  2. permissive（对应0）就是Selinux有效，但是即使你违反了策略的话它让你继续操作，但是把你的违反的内容记录下来。在我们开发策略的时候非常的有用。相当于Debug模式。
  3. enforcing（对应1）就是你违反了策略，你就无法继续操作下去。

查看当前用户security context
``` bash
[root@localhost ~]# id -Z
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[root@localhost ~]# id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```
**说明** 这里虽然是ROOT用户，但也只是在一般的ROLE和staff_t里运行，如果在enforcing模式下，这时的ROOT对于系统管理工作来说，是什么也干不了。

查看当前文件夹下文件security context
``` bash
[root@localhost ~]# ls -Z
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 account
-rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
[root@localhost ~]# ls --lcontext
total 1660
-rw-r--r--.  1 unconfined_u:object_r:admin_home_t:s0 root root      75 May  3 09:34 account
-rw-------.  1 system_u:object_r:admin_home_t:s0 root root    1124 Apr 17 07:17 anaconda-ks.cfg
[root@localhost ~]# ls --scontext
unconfined_u:object_r:admin_home_t:s0 account
system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
```

查看运行进程的security context
``` bash
[root@localhost ~]# ps -eZ
LABEL                             PID TTY          TIME CMD
system_u:system_r:init_t:s0         1 ?        00:00:11 systemd
system_u:system_r:kernel_t:s0       2 ?        00:00:00 kthreadd
system_u:system_r:kernel_t:s0       3 ?        00:00:06 ksoftirqd/0
```

chcon更该文件安全上下文
``` bash
[root@localhost ~]# ls --context hello 
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 hello
[root@localhost ~]# chcon -t etc_t hello 
[root@localhost ~]# ls --context hello 
-rw-r--r--. root root unconfined_u:object_r:etc_t:s0   hello
可以help查看帮助
[root@localhost ~]# chcon --help
Usage: chcon [OPTION]... CONTEXT FILE...
  or:  chcon [OPTION]... [-u USER] [-r ROLE] [-l RANGE] [-t TYPE] FILE...
  or:  chcon [OPTION]... --reference=RFILE FILE...
```
**说明** `ls --context hello`   `ls -lZ hello`  `ls -Z hello` 作用都是查看文件安全策略的[上下文|环境|语境]


恢复原来的文件标签
``` bash
[root@localhost ~]# restorecon hello
[root@localhost ~]# ls --context hello 
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 hello
```

重置文件security context
``` bash
[root@localhost ~]# restorecon hello
下面是用法
usage:  restorecon [-iFnprRv0] [-e excludedir] pathname...
usage:  restorecon [-iFnprRv0] [-e excludedir] -f filename
```

关闭selinux,永久关闭,需要重启`reboot`或者`init 6`
``` bash
[root@localhost ~]#vi /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
#SELINUX=enforcing
SELINUX=disalbed
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```