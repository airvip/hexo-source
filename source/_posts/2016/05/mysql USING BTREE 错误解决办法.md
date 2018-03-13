---
title: mysql USING BTREE 错误解决办法
date: 2016-05-30 21:43:15
tags:
    - Mysql
---

> 假设能像游戏一样，回到上一个游戏存档点重新做选择的话，人生会发生改变吗？答案是否定的。

You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'USING BTREE) ENGINE=MyISAM AUTO_INCREMENT=2 DEFAULT CHARSET=utf8

## Btree Error

b-tree 是mysql 索引默认使用的数据结构。

今天导入破解版的小猪cms的sql文件,报了btree的错误。如图：
![btree error](/img/201605/btree/btree_error.jpg)


我查了一下自己的数据库版本，好像有点低了，低了也要解决问题啊！

```bash
mysql> select version();
+---------------------+
| version()           |
+---------------------+
| 5.0.22-community-nt |
+---------------------+
1 row in set (0.00 sec)
```

修改代码
``` bash
 KEY `mer_id` USING BTREE (`mer_id`)
```

修复结果
``` bsah
DROP TABLE IF EXISTS `pigcms_source_material`;
CREATE TABLE `pigcms_source_material` (
  `pigcms_id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `it_ids` varchar(500) NOT NULL COMMENT '图文表id集合',
  `mer_id` int(10) unsigned NOT NULL,
  `dateline` int(10) unsigned NOT NULL,
  `type` tinyint(1) unsigned NOT NULL COMMENT '图文类型（0：单图文，1：多图文）',
  PRIMARY KEY (`pigcms_id`),
  KEY `mer_id` USING BTREE (`mer_id`)
) ENGINE=MyISAM AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```
问题解决Y(^_^)Y
