---
title: 算法之选择排序的Python实现及分析
date: 2018-08-07 12:00:00
tags: 
    - 算法
---

> 你动她一下试试，我保证你见不到明天的太阳！

选择排序（Selection sort）是一种简单的排序算法。每一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在待排序列的起始位置，直到全部待排序的数据元素排完。 

<!-- more -->

冒泡排序通过依次交换相邻两个顺序不合法的元素位置，从而将当前最小（最大）元素放到合适的位置。
选择排序每遍历一次都记住了当前最小（最大）元素的位置，仅需一次交换操作即可将其放到合适的位置。

# Python 实现

```
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

def select_sort(nums):
    for i in range(len(nums) - 1):
        now_min = i
        for j in range(i+1, len(nums)):
            if nums[now_min] > nums[j]:
                now_min = j
        nums[i], nums[now_min] = nums[now_min], nums[i]
    return nums

if __name__ == "__main__":
    list_nums = [32, 24, 19, 55, 30, 22, 8]
    list_res = select_sort(list_nums)
    print(list_res)
```

运行结果

```
[8, 19, 22, 24, 30, 32, 55]
```

# 程序分析

第一遍选择：初始列表 `[32, 24, 19, 55, 30, 22, 8]`，默认最小值为：32

32 与 24 比较，32 > 24 ,设置最小值为：24
24 与 19 比较，24 > 19 ,设置最小值为：19
19 与 55 比较，19 < 55 ,最小值不做任何修改
19 与 30 比较，19 < 30 ,最小值不做任何修改
19 与 22 比较，19 < 22 ,最小值不做任何修改
19 与 8 比较，19 > 8 ,设置最小值为：8

将最小值与待排序列表第一个元素交换，此时 8 就被筛选到待排序列表的第一个值（列表的第一个值）。

第二遍选择：经过第一遍选择后的列表 `[8,  24, 19, 55, 30, 22, 32]`，默认设置最小值：24

24 与 19 比较，24 > 19 ,设置最小值为：19
19 与 55 比较，19 < 55 ,最小值不做任何修改
19 与 30 比较，19 < 30 ,最小值不做任何修改
19 与 22 比较，19 < 22 ,最小值不做任何修改

将最小值与待排序列表第一个元素交换，此时 19 就被筛选到待排序列表的第一个值（列表的第二个值）。

这样经过多次选择，最终就可以得到一个升序的列表 `[8, 19, 22, 24, 30, 32, 55]`
