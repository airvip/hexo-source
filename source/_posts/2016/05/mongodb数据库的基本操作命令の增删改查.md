---
title: mongodb数据库的基本操作命令の增删改查
date: 2016-05-28 17:54:44
tags:
    - Mongodb
---

> 美好的人眼裡映出的世界也是美好的。

好记性不如烂笔头，现在将mongodb的一些基本操作记录下来，方便之后的学习与查看。

<!-- more -->

## 帮助命令

使用帮助命令
``` bash
[root@localhost ~]# mongo --help
MongoDB shell version: 2.6.11
usage: mongo [options] [db address] [file names (ending in .js)]
db address can be:
  foo                   foo database on local machine
  192.169.0.5/foo       foo database on 192.168.0.5 machine
  192.169.0.5:9999/foo  foo database on 192.168.0.5 machine on port 9999
................省略..................
```

## 连接操作
连接mongodb数据库
``` bash
[root@localhost ~]# mongo 127.0.0.1:27017
//查看数据库
> show dbs
admin  (empty)
local  0.078GB
//使用某个数据库,可以不提前创建，mongo会根据需要自己创建
> use blog
switched to db blog
```

## 新增
插入数据
``` bash
> db.blog_collection.insert({x:1})
WriteResult({ "nInserted" : 1 })
> for(i=3;i<10;i++)db.blog_collection.insert({x:i})
WriteResult({ "nInserted" : 1 })
```

## 查看
查看集合
``` bash
> show collections
blog_collection
system.indexes
//或者使用，结果一样
> show tables
```

查看数据
``` bash
> db.blog_collection.find()
{ "_id" : ObjectId("5749648074e4bff8d390718b"), "x" : 1 }
> db.blog_collection.find({x:1})
{ "_id" : ObjectId("5749648074e4bff8d390718b"), "x" : 1 }
//1正序；-1倒序（跳过3条，显示2条正序）
> db.blog_collection.find().skip(3).limit(2).sort({x:1})
{ "_id" : ObjectId("574968b774e4bff8d3907194"), "x" : 4 }
{ "_id" : ObjectId("574968b774e4bff8d3907195"), "x" : 5 }
```
查看数据数量
``` bash
> db.blog_collection.find().count()
9
```

## 修改
更新数据
``` bash
> db.blog_collection.update({x:2},{x:22})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
---------------------------------------------------------------------------
//更新部分数据，先插入一条数据
> db.blog_collection.insert({x:100,y:100,z:100})
WriteResult({ "nInserted" : 1 })
//将刚刚插入的数据的y值更新为99
> db.blog_collection.update({x:100},{$set:{y:99}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
----------------------------------------------------------------------------
//更新不存在的数据时，插入一条，使用update第三个参数true
> db.blog_collection.update({x:101},{y:100},true)
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("5749cfb5cab631c0bf529868")
})
//查看刚刚插入的数据
> db.blog_collection.find({y:100})
{ "_id" : ObjectId("5749cfb5cab631c0bf529868"), "y" : 100 }
---------------------------------------------------------------------------
//mongo当有多条相同的数据是，默认只更新第一条数据,现插入三条数据
> for(i=0;i<3;i++)db.blog_collection.insert({a:1})
WriteResult({ "nInserted" : 1 })
> db.blog_collection.find({a:1})
{ "_id" : ObjectId("5749d0ff74e4bff8d390719b"), "a" : 1 }
{ "_id" : ObjectId("5749d0ff74e4bff8d390719c"), "a" : 1 }
{ "_id" : ObjectId("5749d0ff74e4bff8d390719d"), "a" : 1 }
> db.blog_collection.update({a:1},{a:2})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.blog_collection.find({a:1})
{ "_id" : ObjectId("5749d0ff74e4bff8d390719c"), "a" : 1 }
{ "_id" : ObjectId("5749d0ff74e4bff8d390719d"), "a" : 1 }
> db.blog_collection.find({a:2})
{ "_id" : ObjectId("5749d0ff74e4bff8d390719b"), "a" : 2 }
--------------------------------------------------------------------
//现在还有两条没有更新，若要全部更新，使用set,并使用第4个参数
> db.blog_collection.update({a:1},{$set:{a:2}},false,true)
WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })
> db.blog_collection.find({a:2})
{ "_id" : ObjectId("5749d0ff74e4bff8d390719b"), "a" : 2 }
{ "_id" : ObjectId("5749d0ff74e4bff8d390719c"), "a" : 2 }
{ "_id" : ObjectId("5749d0ff74e4bff8d390719d"), "a" : 2 }
```

## 删除
删除数据
``` bash
//不允许不传参数，与update不同默认删除查到的所有数据
> db.blog_collection.remove({a:2})
WriteResult({ "nRemoved" : 3 })
```

删除集合
``` bash
> db.blog_collection.drop()
true
> show tables
system.indexes
```

删除数据库(使用着哪个数据库删除哪个数据库)
``` bash
>db.dropDatabase()
```