---
title: 在redis命令行下查看中文
date: 2016-11-02 07:00:32
tags:
    - Redis
---

> At least I tried,At least I did that

Redis 是完全开源免费的，遵守 BSD 协议，是一个高性能的 key-value 数据库。在使用命令行操作时，如果查看内容中包含中文，会显示16进制的字符串。如：`"\xe9\x98\xbf\xe5\xb0\x94\xe7\xbb\xb4\xe5\xa5\x87"`。

<!-- more -->

平时我们使用 Redis 命令行查看存储内容是非常方便快捷的。如下

``` Bash
C:\Users\sdqhw>redis-cli
127.0.0.1:6379> get en_name
"airvip"
127.0.0.1:6379> get name
"\xe9\x98\xbf\xe5\xb0\x94\xe7\xbb\xb4\xe5\xa5\x87"
127.0.0.1:6379>
```

![redis-cli_get](/img/201611/redis-cli/redis-cli_get.jpg)


当获取的内容中使用了中文时，我们得到的16进制字符串很难看出内容是什么，那能不能直接显示中文呢？淡然是可以的!

# Redis-cli显示中文

## Windows 操作系统

1、按 WIN + R 组合键，弹出窗口输入 cmd 回车。

2、鼠标放在命令窗口的标题部分右键，在弹出的右键菜单上面选择【属性】

![cmd_attr](/img/201611/redis-cli/cmd_attr.jpg)

3、如果当前代码页不是 65001(UTF-8),我们在命令行输入 `chcp 65001`, 把编码修改成 UTF-8

``` Bash
C:\Users\sdqhw>chcp 65001
Active code page: 65001 # 修改成功
```

4、现在通过 `redis-cli --raw` 进入 redis 命令行

``` Bash
C:\Users\sdqhw>redis-cli --raw
127.0.0.1:6379> get name
阿尔维奇
127.0.0.1:6379> get en_name
airvip
127.0.0.1:6379>
```

![redis-cli_utf-8_get](/img/201611/redis-cli/redis-cli_utf-8_get.jpg)

## Linux(Ubuntu) 操作系统

方法一：

``` Bash
$ redis-cli --raw
127.0.0.1:6379> get name
阿尔维奇
127.0.0.1:6379> get en_name
airvip
127.0.0.1:6379>
```

方法二：

``` bash
$ echo -e `redis-cli get name`
阿尔维奇
```


