---
title: Linux 之用户与组的相关操作
date: 2017-02-17 23:09:15
tags:
    - Linux
---

> 梦想不会逃跑，会逃跑的只有人。

该篇文章主要记录 Linux 中用户与组的相关知识点，一般 GUN/Linux 都是通过用户与组来实现访问控制的--包括对文件访问、设备使用的控制。Linux 默认的访问控制机制相对简单直接，不过还有一些更加高级的机制，包括 ACL 和 LDAP authentication.

<!-- more -->

特别提醒：Linux 中一切皆文件。

在 Linux 用户系统中存在两类组。第一类是主要用户组，第二类是附加用户组。

# 用户与组的对应关系

一对一：某个用户可以是某个组的唯一成员；
多对一：多个用户可以是某个唯一的组的成员，不再归属其它用户组；
一对多：某个用户可以是多个用户组的成员；
多对多：多个用户对应多个用户组，并且几个用户可以是归属相同的组；

# 用户与组的配置文件

既然在 linux 中一切皆文件，那用户组的信息存储在那个文件中呢？

与用户（user）相关的配置文件
``` 
/etc/passwd	  #用户账户信息
/etc/shadow	  #保存用户安全信息
```

与用户组（group）相关的配置文件
``` 
/etc/group	    #定义用户所属的组
/etc/gshadow	#保存组账号的安全信息
```

# 先聊用户(user)管理

新建用户
``` bash
#useradd 用户名
$ sudo useradd yaya #新建用户 yaya
$ sudo passwd yaya #给 yaya 用户设置密码，虽然不是必须的，还是强烈建议设置密码，方便作为可登录用户 
```
`useradd -m -g 组名 新建用户名`
说明 
* -m:自动建立用户家目录
* -g:指定用户所在的组，否则会建立一个和用户同名的组

查看系统上所有用户
``` bash
$ cat /etc/passwd
```
每一行代表一个用户，格式如下，每行分七个部分，用英文冒号“:”分开
``` bash
account:password:UID:GID:GECOS:directory:shell
```
说明
* account：用户名，不能为空，而且要符合标准的 *NIX 命名规则。
* password：加密的密码，可以使用一个小写的"x"（不带括号）表示密码保存在/etc/shadow文件里。
* UID：每个用户都有一个对应的UID（用户ID）。Ubuntu里面，第一个非root用户的默认UID是1000，后续创建的用户UID也应大于1000。
* GID：每个用户的首要GID（组ID）。特定用户的GID应该属于指定的首要组，组的ID数值列在/etc/group文件里。
* GECOS：可选的注释字段，通常记录用户全名
* directory：用于登录命令设置$HOME环境变量。某些服务的用户主目录设置为"/"是安全的，但不建议普通用户设置为此目录。
* shell：是用户默认登录的shell，通常是Bash，还可选择其他的命令解释器，默认是"/bin/bash"（不带括号），如果你用的是别的shell，在这里设置其路径，此部分是可选的，可留空。

查看 yaya 用户的信息
``` bash
$ id yaya
uid=1001(yaya) gid=1001(yaya) 组=1001(yaya)
```
可以看到新加的 yaya 用户只有一个主组

将 yaya 用户添加附加用户组 sudo 组，即：给用户 yaya 新增新的所属组而不改变他的主组
``` bash
$ sudo usermod -a -G sudo yaya
$ id yaya
uid=1001(yaya) gid=27(sudo) 组=27(sudo)
```
`usermod -a -G 新增的组名 用户名`
说明：
-a:代表append,也就是将用户添加到新用户组中而不必离开原有的其他用户组。需要配合 -G 使用

修改 yaya 用户的主组为 adm 组
``` bash
#usermod -g 新的主组名 用户名
$ sudo usermod -g adm yaya
$ id yaya
uid=1001(yaya) gid=4(adm) 组=4(adm),27(sudo)
```

修改 yaya 附属组列表
``` bash
$ sudo usermod -G airvip,yaya,sudo yaya
$ id yaya
uid=1001(yaya) gid=4(adm) 组=4(adm),27(sudo),1000(airvip),1001(yaya)
```

删除用户
``` bash
#userdel -r 用户名 
$ sudo userdel -r yaya #删除用户yaya
```
`userdel -r 用户名`
说明
* -r:表示一并删除用户主目录和邮件。


# 再谈组(group)管理

查看用户所在的组名(不常用)
``` bash
$ groups 用户名  #若省略用户名，默认显示当前用户所在组。
```
查看用户所在组信息(常用)
``` bash
$ id 用户名 #包括用户UID以及相关用户组GID
```

查看系统中所有组信息
``` bash
$ cat /etc/group  
```

新建组
``` bash
#groupadd 组名
$ sudo groupadd dev    #新建dev组
```

更改组名，不变更GID
``` bash
#groupmod -n 新组名 旧组名
$ sudo groupmod -n dev1 dev
```

删除组
``` bash
#groupdel 组名
$ sudo groupdel dev     #删除dev组
```


# 用户与组的操作

如果操作的对象是文件，可以不使用 -R 参数，-R 表示递归修改

把 learn 文件夹所属组修改为 dev 组
``` bash
#chgrp -R 要修改成的用户名 要修改的文件或者文件夹
$ sudo chgrp -R dev ./learn
```

把 learn 文件夹所属用户修改为 root 用户
``` bash
# chown -R 要修改成的用户名 要修改的文件或者文件夹
$ sudo chown -R root ./learn
```

把 learn 文件夹同时修改它的用户为 airvip ,组为 airvip
``` bash
#chown -R 要修改成的用户名.要修改成的用户名  要修改的文件或者文件夹
$ sudo chown -R airvip.airvip ./learn
```