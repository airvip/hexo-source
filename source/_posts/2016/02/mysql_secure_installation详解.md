---
title: mysql_secure_installation详解
date: 2016-02-01 18:24:56
tags:
    - MySQL
---

> 如果你还记得我们的约定，我在之前的路口等你。

由于安装了MySQL5.7.12，安全配置向导mysql_secure_installation与之前版本有点出入，在此记述下各项配置的含义，方便自己以后查看

<!-- more -->

## 密码设置

安装了mysql之后，初次运行mysql之时

查看旧密码
```
[root@localhost ~]# cd /var/log
[root@localhost log]# cat mysqld.log
2016-02-01T08:15:05.074266Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2016-02-01T08:15:07.221159Z 0 [Warning] InnoDB: New log files created, LSN=45790
2016-02-01T08:15:08.122276Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2016-02-01T08:15:08.455987Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: fbc71b85-103d-11e6-8712-0800271b66d9.
2016-02-01T08:15:08.588159Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2016-02-01T08:15:08.594076Z 1 [Note] A temporary password is generated for root@localhost: BCFE=og?h9%X
```

密码就是BCFE=og?h9%X

根据MySQL官方手册，建议安装完MySQL数据库后，使用下面的命令来

增强数据库的安全性
```
[root@localhost log]# mysql_secure_installation
确保mysql数据库部署安全。
Securing the MySQL server deployment.
输入root用户的密码
Enter password for user root: 
现有的root用户的密码已过期。请设置新密码。
The existing password for the user account root has expired. Please set a new password.
新密码：
New password: 
重新输入新密码
Re-enter new password: 
。。。失败！错误：你输入的密码不符合现在的政策要求，我输入的是123456，旧版本就可以，现在安全第一
 ... Failed! Error: Your password does not satisfy the current policy requirements
新密码：
New password: 
重新输入新密码，这次机智了输入了Iloveyou.123456，哭瞎，这么复杂的密码我怎么能记住
Re-enter new password: 
密码检验插件已经安装在服务器上
The 'validate_password' plugin is installed on the server.
之后的步骤将和插件配置一起运行
The subsequent steps will run with the existing configurationof the plugin.
使用现在的root用户密码
Using existing password for root.
鉴定密码强度100分
Estimated strength of the password: 100 
是不是改密吗，必须的是啊
Change the password for root ? ((Press y|Y for Yes, any other key for No) : y
新密码
New password: 
重新输入密码
Re-enter new password: 
鉴定密码强度100分
Estimated strength of the password: 100 
你是否想继续使用提供的密码，是
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
默认，有一个mysql匿名用户表，允许没有创建用户的任何人登录mysql，
这是是为了测试，是安装顺利进行。在生产环境中要删除这个瓜娃子。
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.
删除匿名用户吗？是
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
成功
Success.

当然，root必须可以从本地连接数据库。来确保某些人从网上不能猜测这root的密码。
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.
不允许远程登录？最好是，我为图方便空格
Disallow root login remotely? (Press y|Y for Yes, any other key for No) :  

 ... skipping.
默认，mysql给了一个test测试数据库。只是为了测试，在生产环境中要移除。
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

移除test数据库及其的访问吗？最好是，我要测试空格
Remove test database and access to it? (Press y|Y for Yes, any other key for No) :  

 ... skipping.
重载授权表将确保所有的变化使其立即生效
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
重新载入授权表吗？是
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
成功
Success.
一切结束
All done! 
```

**说明**

mysql_secure_installation配置中，我没移除test数据库，可是当我看看数据库是还是没有了。

原因不明，不知是否操作有问题。