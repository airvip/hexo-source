---
title: Mysql对某字段存在多个标签时的搜索
date: 2019-03-22 10:24:55
tags: 
    - Mysql
---

> 风停之后再扬帆，船绝不会前行。

一个用户存在多个标签，如果把用户和标签之前的关系用一张表存起来，在查询的时候就需要使用join操作，如果不想要使用join操作，我们可以把标签存在用户表记录的某个字段来实现。

<!-- more  -->

一般我们如果把多个标签存在同一个字段中，存储格式为 `帅哥,小鲜肉,gay` 

# 先看表结构及数据

``` bash
-- ----------------------------
-- Table structure for tags
-- ----------------------------
DROP TABLE IF EXISTS `tags`;
CREATE TABLE `tags` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'pk',
  `name` varchar(50) NOT NULL DEFAULT '' COMMENT '标签名',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'pk',
  `name` varchar(20) NOT NULL DEFAULT '' COMMENT '姓名',
  `tags` varchar(255) CHARACTER SET utf8mb4 NOT NULL DEFAULT '' COMMENT '标签',
  `tags_id` varchar(255) CHARACTER SET utf8mb4 NOT NULL DEFAULT '' COMMENT '标签id',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;

```

# 虚拟数据存储如下

tag表中存在的一点虚拟数据

| id | name |
|---- |---- |
| 1 | 哥哥 |
| 2 | 小哥哥 |
| 3 | 小鲜肉 |
| 4 | 美女 |
| 5 | 靓妹 |
| 6 | 非主流 |

user表中存在的一点虚拟数据

| id | name | tags | tags_id|
|---- |---- |---- |---- |
| 1 | 一言 | 哥哥,小鲜肉,非主流 | 1,3,6 |
| 2 | 良语 | 小哥哥,哥哥,小鲜肉 | 2,1,3 |
| 3 | 叁元 | 小鲜肉 | 3 |
| 4 | 思恋 | 美女,靓妹 | 3,5,6 |

# 查询解决

数据与结构基本上就是这个样子，现在需要我们查询存个【哥哥】标签的用户有哪些？
我们的同学就会抱怨说，这是什么鬼设计的数据库，怎么会这样存数据呢？我用like搜索可能搜出来的数据不准确啊！的确，这种的数据结构用like来做检索确实有问题，这种一个字段存在多个标签的搜索其实可以使用 Mysql 为我们提供的 find_in_set 来解决。

```
SELECT * FROM `user` WHERE FIND_IN_SET('哥哥',tags);
```

即便是用tag的 id 来检索也是一样的

```
SELECT * FROM `user` WHERE FIND_IN_SET(1,tags_id);
```

现在如果要搜索同时存在【小哥哥】和【小鲜肉】的用户我们可以这样操作

```
SELECT * FROM `user` WHERE FIND_IN_SET(3,tags_id) AND FIND_IN_SET(2,tags_id);
```

**下面这种骚操作是错误的**

```
SELECT * FROM `user` WHERE FIND_IN_SET('2,3',tags_id);
```
