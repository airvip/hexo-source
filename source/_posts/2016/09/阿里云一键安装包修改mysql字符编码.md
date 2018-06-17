---
title: 阿里云一键安装包修改mysql字符编码
date: 2016-09-01 11:52:32
tags:
    - Mysql
---

> 要是因为烦恼很痛苦，就选择了轻松的选项，将来一定会后悔。

mysql创建数据库时指定编码很重要，制定数据库的编码可以很大程度上避免倒入导出带来的乱码问题。

<!-- more -->

现在网页数据一般采用UTF8编码，而数据库默认为latin 。我们可以通过修改数据库默认编码方式为UTF8来减少数据库创建时的设置，也能最大限度的避免因粗心造成的乱码问题。

# 查看mysql信息

我们可以通过下面的命令查看我们mysql的编码设置

``` bash
mysql> show variables like 'char%';
结果如下：
+--------------------------+----------------------------------------------+
| Variable_name            | Value                                        |
+--------------------------+----------------------------------------------+
| character_set_client     | utf8                                         |
| character_set_connection | utf8                                         |
| character_set_database   | latin1                                       |
| character_set_filesystem | binary                                       |
| character_set_results    | utf8                                         |
| character_set_server     | latin1                                       |
| character_set_system     | utf8                                         |
| character_sets_dir       | /alidata/server/mysql-5.6.21/share/charsets/ |
+--------------------------+----------------------------------------------+
```

看一下自己的mysql版本

``` bash
mysql> select version();
+------------+
| version()  |
+------------+
| 5.6.21-log |
+------------+
```

# 设置编码

临时修改的解决代码（不怎么喜欢,次次修改，还很麻烦）

``` bash
mysql>SET character_set_client = utf8;
mysql>SET character_set_connection = utf8;
mysql>SET character_set_database = utf8;
mysql>SET character_set_results = utf8;
mysql>SET character_set_server = utf8;
```

基本就是你想修改哪一个，你就set设置哪一个。

想要永久修改，一劳永逸就要`从my.cnf(linux)` || `my.ini(windows)`下手(标签下没有的添加，有的修改)

路径`/alidata/server/mysql/my.cnf`

``` bash
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html
[mysqld]
character-set-server=utf8 
```

保存之后重启服务

``` bash
[root@localhost ~]# service mysqld restart
```

# 检验结果

现在再看一下字符编码是否修正了

``` bash
mysql> show variables like 'char%';
+--------------------------+----------------------------------------------+
| Variable_name            | Value                                        |
+--------------------------+----------------------------------------------+
| character_set_client     | utf8                                         |
| character_set_connection | utf8                                         |
| character_set_database   | utf8                                         |
| character_set_filesystem | binary                                       |
| character_set_results    | utf8                                         |
| character_set_server     | utf8                                         |
| character_set_system     | utf8                                         |
| character_sets_dir       | /alidata/server/mysql-5.6.21/share/charsets/ |
+--------------------------+----------------------------------------------+
``` 

OK,这样数据库的编码就设置好了