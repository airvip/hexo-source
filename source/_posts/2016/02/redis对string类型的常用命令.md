---
title: redis对string类型的常用命令
date: 2016-02-12 12:02:00
tags:
    - Redis
---

> 在茫茫人海中，同样是高中生，同乘7点50分的电车，看着同一片海…那个瞬间不就是如奇迹般的邂逅吗。

学习一下redis对字符串类型的常用命令，总结学习记录，以便日后查看。

<!-- more -->

# string 操作

进入redis数据库
```
[root@localhost ~]# redis-cli
```

设置key值
```
127.0.0.1:6379> set name wzb
OK
```

一次设置多个key
```
127.0.0.1:6379> mset name wzb age 24 addr shanxi sex 1
OK
```

获取key值
```
127.0.0.1:6379> get name
"wzb"
```

获取多个key值
```
127.0.0.1:6379> mget name age addr sex
1) "wzb"
2) "24"
3) "shanxi"
4) "1"
```

删除多个key
```
127.0.0.1:6379> del name age addr
(integer) 3
```

查看key是否存在
```
127.0.0.1:6379> exists name
(integer) 1
```

获取key类型
```
127.0.0.1:6379> type age
string
```

keys 匹配
```
127.0.0.1:6379> keys *
1) "age"
2) "addr"
3) "name"
127.0.0.1:6379> keys a*
1) "age"
2) "addr"
127.0.0.1:6379> keys *e
1) "age"
2) "name"
```

重命名key
```
127.0.0.1:6379> rename name names
OK
```

查看当前数据库的key数量
```
127.0.0.1:6379> dbsize
(integer) 3
```

设置key的过期时间（秒）
```
127.0.0.1:6379> expire names 3
(integer) 1
```

返回key的剩余过期秒数(-1一直不过期)
```
127.0.0.1:6379> ttl name
(integer) -1
```

选择数据库,默认为0，配置文件有16，标号[0-15]
```
127.0.0.1:6379>select 1
```

将key从当前数据库移动到指定的数据库
```
127.0.0.1:6379>move name 1
```

删除当前数据库中所有的key
```
127.0.0.1:6379>flushdb
```

删除所有数据库中所有的key
```
127.0.0.1:6379>flushall
```

没有key,创建为1；有key值，必须是interger类型，key值增加1
```
127.0.0.1:6379> incr num
(integer) 
```
1
decr key 值减一
```
127.0.0.1:6379> get age
"24"
127.0.0.1:6379> decr age
(integer) 23
127.0.0.1:6379> get age
"23"
```

incrby key可以加定值
```
127.0.0.1:6379> incrby age 2
(integer) 25
```

decrby key可以减定值
```
127.0.0.1:6379> decrby age 3
(integer) 22
```

append key value 给指定key追加value
```
127.0.0.1:6379> get addr
"shanxi"
127.0.0.1:6379> append addr xianyang
(integer) 14
127.0.0.1:6379> get addr
"shanxixianyang"
```

截取某一key值 包括开始位置   结束位置 （下标0开始）
```
127.0.0.1:6379> substr addr 6 13
"xianyang"
```