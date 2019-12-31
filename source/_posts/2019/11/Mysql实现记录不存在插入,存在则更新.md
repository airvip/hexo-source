---
title: Mysql实现记录不存在插入,存在则更新
date: 2019-11-04 16:01:23
tags: 
    - Mysql
---

> 如果累了,就休息一下，休息好了，再出发。

在我们开发业务系统的时候，经常会遇到先判断资源是否存在，如果不存在，就插入数据，如果存在，就更新数据。

如果在业务层处理，我们就需要操作两次数据库：

```
1. 查询数据库记录
2. 判断该记录是否存在
3. 选择性写入数据 或 更新数据
```

<!-- more  -->

其实，我们可以直接在数据层处理，因为 Mysql 语法原生支持这种操作，所以我们通过 INSERT ... ON DUPLICATE KEY UPDATE ... 语法操作起来特别方便 。

操作原理

如果插入的数据记录，在数据表中的记录已经存在了相同 UNIQUE索引 或 PRIMARY KEY 的数据，就会对已存在的记录执行 UPDATE 操作。

# 单字段确定唯一索引

表结构及操作

```
-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `uni_id` int(10) unsigned NOT NULL DEFAULT 0 COMMENT 'key',
  `name` varchar(20) NOT NULL DEFAULT '' COMMENT '姓名',
  `age` tinyint(3) unsigned NOT NULL DEFAULT 0 COMMENT '年龄',
  PRIMARY KEY (`uni_id`),
  UNIQUE KEY `index_id` (`uni_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

```

SQL 操作

```
INSERT INTO table (`uni_id`,`name`,`age`) VALUES (55555,'阿尔维奇',24) ON DUPLICATE KEY UPDATE age=age+1; 
```

说明：如果表中没有 (55555,'阿尔维奇',24) 这条数据，则会直接插入；如果存在 uni_id 为 55555 的记录，则年龄加 1 。


SQL 操作

```
INSERT INTO table (`uni_id`,`name`,`age`) VALUES (55555,'阿尔维奇',24),(66666,'阿尔@维奇',24) ON DUPLICATE KEY UPDATE age=age+1; 
```

等效于
```
INSERT INTO table (`uni_id`,`name`,`age`) VALUES (55555,'阿尔维奇',24) ON DUPLICATE KEY UPDATE age=age+1; 
INSERT INTO table (`uni_id`,`name`,`age`) VALUES (66666,'阿尔@维奇',24) ON DUPLICATE KEY UPDATE age=age+1; 
```

# 多字段确定唯一索引

表结构如下

```
-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `name` varchar(20) NOT NULL DEFAULT '' COMMENT '姓名',
  `sex` tinyint(3) unsigned NOT NULL DEFAULT 0 COMMENT '1男2女0未知',
  `addr` varchar(50) NOT NULL DEFAULT '' COMMENT '地址',
  `age` tinyint(3) unsigned NOT NULL DEFAULT 0 COMMENT '年龄',
  PRIMARY KEY (`name`,`addr`),
  UNIQUE KEY `index_id` (`name`,`addr`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

```

SQL 操作

```
INSERT INTO table (`name`,`sex`,`addr`,`age`) VALUES ('阿尔维奇',1,'地球',24) ON DUPLICATE KEY UPDATE age=age+1; 
```




