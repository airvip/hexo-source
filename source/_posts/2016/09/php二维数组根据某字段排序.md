---
title: php二维数组根据某字段排序
date: 2016-09-11 00:47:15
tags:
    - Php
---

> 繁花落尽，明月映着谁老去…

最近一直处理工作中遇到的一些问题，就懒得打理自己的知识点，现在就在这里记录一个php中经常会遇到的对二维数组排序的问题。

<!-- more -->

# 需求

需要实现对从数据库查找出的二维数组按其中的某一字段排序.

原始数据
``` php
$arr     = [
    0 => [
      'depart' => '手术科',
      'depart_py' => 'shushuke',
      'level' => '1',
    ],
    1 => [
        'depart' => '口腔科',
        'depart_py' => 'keqiangke',
        'level' => '1',
    ],
    3 => [
        'depart' => '门诊科',
        'depart_py' => 'menzhenke',
        'level' => '1',
    ],
    4 => [
        'depart' => '医疗科',
        'depart_py' => 'yiliaoke',
        'level' => '1',
    ],
    5 => [
        'depart' => '鼻科',
        'depart_py' => 'bike',
        'level' => '1',
    ],
];
```


# 编程实现
**根据部门拼音做正序排列。**
``` bash
// 构建根据那个排序的数组
$tmp = [];
foreach($arr as $k=>&$v)$tmp[]= &$v['depart_py'];
unset($v);

// 按拼音倒序
// array_multisort($tmp,SORT_DESC,$arr);
// 按拼音正序
array_multisort($tmp,SORT_ASC,$arr);

// 打印结果
print_r($arr);
```

运行结果
``` bash
Array
(
    [0] => Array
        (
            [depart] => 鼻科
            [depart_py] => bike
            [level] => 1
        )

    [1] => Array
        (
            [depart] => 口腔科
            [depart_py] => keqiangke
            [level] => 1
        )

    [2] => Array
        (
            [depart] => 门诊科
            [depart_py] => menzhenke
            [level] => 1
        )

    [3] => Array
        (
            [depart] => 手术科
            [depart_py] => shushuke
            [level] => 1
        )

    [4] => Array
        (
            [depart] => 医疗科
            [depart_py] => yiliaoke
            [level] => 1
        )

)
```