---
title: Mysql 根据一个表数据更新另外一个表
date: 2018-03-20 11:14:19
tags: 
    - Mysql
---

> 这个世界上有无法赎清的罪,但是,却没有不能去爱的人啊。

有些时候因为数据库设计问题，需要将一张表的部分字段缓存到另一张表中，这就需要我们根据已有的数据去更新，下面我们来看看如何通过一条 Sql 来叨叨我们的需求。

<!-- more -->

# 表结构

下面的表部分字段已被过滤掉一部分

``` bash
-- ----------------------------
-- Table structure for comment
-- ----------------------------
DROP TABLE IF EXISTS `comment`;
CREATE TABLE `comment` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'pk',
  `question_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '问题主键',
  `score` float unsigned NOT NULL DEFAULT '5' COMMENT '分值',
  `status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '0待审核1已审核-1作废',
  `create_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '0系统 非0用户',
  `update_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '0系统 非0管理',
  `create_time` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  `update_time` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `checker` varchar(20) NOT NULL DEFAULT '' COMMENT '审核人',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COMMENT='评价表';;

-- ----------------------------
-- Table structure for questions
-- ----------------------------
DROP TABLE IF EXISTS `questions`;
CREATE TABLE `questions` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'pk',
  `score` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '得分',
  `checker` varchar(20) NOT NULL DEFAULT '' COMMENT '审核人',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COMMENT='问题表';

```

# 需求及实施

具体需求

1. 评论表已有分值
2. 查问题表，避免使用视图及关联
3. 在问题表中也添加分值字段做缓存
4. 将评论表中的分值根据问题主键更新到问题表中

Sql 实施

一列：更新分值

``` bash
UPDATE questions q,comment c 
SET q.score = c.score 
WHERE q.id = c.question_id;
```

两列：更新分值、检验人

``` bash
UPDATE questions q,comment c 
SET q.score = c.score, q.checker = c.checker
WHERE q.id = c.question_id;
```

一列：更新分值(使用子查询更新)
``` bash
update questions q 
set score = (select socre from comment where comment = q.comment);
```