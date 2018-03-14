---
title: mongodb数据库的基本操作命令の索引
date: 2016-05-29 01:33:39
tags:
    - Mongodb
---

> 有一些情感，没办法辜负它。

为了查询的高效性，建立索引是必不可少的一部操作，在该篇中我们将主要学习有关mongodb的索引相关的信息。在此简单记录，方便之后查询使用。

<!-- more -->

## 查看索引
``` bash
> db.bg_user.getIndexes()
[ ]
> db.bg_user.insert({name:'airvip'})
WriteResult({ "nInserted" : 1 })
```

_id索引是绝大多数集合默认建立的索引
``` bash
> db.bg_user.getIndexes()
[
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "blog.bg_user"
	}
]
```
## 创建索引

### 单/多键索引
创键单键索引(1正序；-1倒序)
单键索引：值为一个单一的值，例如：字符串，数字或者日期
``` bash
> db.bg_user.ensureIndex({name:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

多键索引（单键索引与多建索引创建形式相同，区别在于字段的值不同）
多键索引：只具有多个记录，例如数组
创建多键索引（对下面插入的数据，name就构成了多键索引）
``` bash
> db.bg_user.insert({name:['airvip','wzb','babybei']})
WriteResult({ "nInserted" : 1 })
```

### 复合索引
对于用户表，name&pass同时建立索引
``` bash
> db.bg_user.insert({name:'wzb',pass:'123456',addr:'shandong'})
WriteResult({ "nInserted" : 1 })
```
创建复合索引
``` bash
> db.bg_user.ensureIndex({name:1,pass:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 2,
	"numIndexesAfter" : 3,
	"ok" : 1
}
```

### 过期索引（ttl索引）
过期索引（ttl索引）:一段时间后过期的索引。索引过期后，相应数据会被删除。（适合：用户登录信息，存储日志）
``` bash
> db.bg_loginlog.ensureIndex({time:1},{expireAfterSeconds:10})
{
	"createdCollectionAutomatically" : true,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```
过期索引实例
``` bash
插入一条数据
> db.bg_loginlog.insert({time:new Date()})
WriteResult({ "nInserted" : 1 })
> db.bg_loginlog.find()
{ "_id" : ObjectId("5749ddb774e4bff8d39071a2"), 
"time" : ISODate("2016-05-28T18:04:39.927Z") }
//过了一段时间，就没了
> db.bg_loginlog.find()
```

过期索引的限制
1. 存储在过期索引字段的值必须是指定的时间类型（必须是ISODate或者ISODate数组,不能使用时间戳，否则不能被自动删除）;
2. 如果指定了ISODate数组，则按照最小的时间进行删除
3. 过期索引不能是复合索引
4. 删除时间不是精确的（删除过程是由后台程序每60秒跑一次，而且删除也需要一些时间，所以存在误差）

<br/>
### 全文索引
创建全文索引
``` bash
db.bg_blog.ensureIndex({key:"text"})
db.bg_blog.ensureIndex({key_1:"text",key_2:"text"})
db.bg_blog.ensureIndex({"$**":"text"})
> db.bg_blog.ensureIndex({"content":"text"})
{
	"createdCollectionAutomatically" : true,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

全文索引实例
``` bash
//插入几条数据
> db.bg_blog.insert({content:"aa bb cc dd ee"})
WriteResult({ "nInserted" : 1 })
> db.bg_blog.insert({content:"aa bb ww yy"})
WriteResult({ "nInserted" : 1 })
> db.bg_blog.insert({content:"aa bb cc zz"})
WriteResult({ "nInserted" : 1 })
//使用全文索引查询
> db.bg_blog.find({$text:{$search:"aa"}})
{ "_id" : ObjectId("5749e10c74e4bff8d39071a6"), "content" : "aa bb cc zz" }
{ "_id" : ObjectId("5749e0f174e4bff8d39071a5"), "content" : "aa bb ww yy" }
{ "_id" : ObjectId("5749e0d674e4bff8d39071a4"), "content" : "aa bb cc dd ee" }
> db.bg_blog.find({$text:{$search:"ww"}})
{ "_id" : ObjectId("5749e0f174e4bff8d39071a5"), "content" : "aa bb ww yy" }
//三个关键词为或查询，只要包含其中一个就返回
> db.bg_blog.find({$text:{$search:"aa bb cc"}})
{ "_id" : ObjectId("5749e10c74e4bff8d39071a6"), "content" : "aa bb cc zz" }
{ "_id" : ObjectId("5749e0f174e4bff8d39071a5"), "content" : "aa bb ww yy" }
{ "_id" : ObjectId("5749e0d674e4bff8d39071a4"), "content" : "aa bb cc dd ee" }
//查找包含aa、bb且不包含cc的记录（为与查询）
> db.bg_blog.find({$text:{$search:"aa bb -cc"}})
{ "_id" : ObjectId("5749e0f174e4bff8d39071a5"), "content" : "aa bb ww yy" }
//查找同时包含aa、bb、cc的文档
> db.bg_blog.find({$text:{$search:"\"aa\" \"bb\" \"cc\""}})
{ "_id" : ObjectId("5749e10c74e4bff8d39071a6"), "content" : "aa bb cc zz" }
{ "_id" : ObjectId("5749e0d674e4bff8d39071a4"), "content" : "aa bb cc dd ee" }
```