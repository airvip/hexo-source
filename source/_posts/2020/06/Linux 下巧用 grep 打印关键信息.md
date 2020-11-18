---
title: Linux 下巧用 grep 打印关键信息
date: 2020-06-16 10:28:32
tags: 
    - Bash
    - Linux
---

> 放手！我需要自己的自由

日志查询是工作中非常重要的一个必备事项，在遇到莫名其妙的问题时，我们可以通过日志非常快速的定位及发现问题，这就需要我们用好日志查询命令。

<!-- more -->

## 关键字匹配

* 打印日志中匹配的 关键字所在行

```
# tail 默认显示最后 10 行到标准输出
tail system.log | grep "调用远程日志"
# 在最后 100 行中匹配关键字  调用远程日志
tail -n 100 system.log | grep "调用远程日志"
```

* 打印日志中匹配的 关键字所在行 及 上一行

```
tail -n 100 system.log | grep -B 1 "调用远程日志"
```

* 打印日志中匹配的 关键字所在行 及 下一行

```
tail -n 100 system.log | grep -A 1 "调用远程日志"
```


* 打印以 关键字开头的所在行

```
tail -n 100 system.log | grep "^调用远程日志"
```

* 打印 不包含关键字所在行

```
tail -n 100 system.log | grep -v "调用远程日志"
```

* 将最新日志打印到屏幕上

```
tail -f system.log
tail -f system.log | grep --line-buffer "调用远程日志"
```

说明：
管道 `|` 是全缓冲的，一般来说 buffer_size 为 4096，有些是 8192。不管具体值多少，只有 buffer_size 满了，才会看到输出。写入文件操作 `>>file` 也是全缓冲的。
`grep` 当带上了 `--line-buffer` 的时候，每输出一行，就刷新一次。
在 unix 里，块设备和普通文件，以及管道都是全缓冲的。

* 统计匹配关键字的次数

```
less 15_system.log | grep "调用远程日志" | wc -l
```

