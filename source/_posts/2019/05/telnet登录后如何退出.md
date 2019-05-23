---
title: telnet登录后如何退出
date: 2019-05-23 09:22:35
tags: 
    - Telnet
---

> 转身之后，我们就再也没有了往来。

一般情况下我们在命令行里面想要结束某些操作可以使用 `ctrl` + `c` 组合键来解决，然而在使用 telnet `telnet ip 端口号` 连接到主机的时候，`ctrl` + `c` 组合键就失灵了，linux/unix 下还会给一些提示，windows 更是什么提示也没有，那应该如何正常的退出呢？且听我娓娓道来。

<!-- more -->

## 友好的 Linux/Unix 提示

linux/unix 下使用 `telnet ip 端口号` 连接主机时会提示 Escape character is '^]'.

对 Escape character is '^]'. 的解释
* ^ 是 `Ctrl` 键， ] 是 `]` 键。
* 按 `Ctrl` + `]` 组合键就可以进入 telnet 命令行了。

在进入 telnet 命令行之后就可以执行 telnet 的命令了，比如想要**退出**，直接在命令行输入 quit 并回车就可以了。

**说明**
虽然 windows 系统没有这个提示，同样也是通过  `Ctrl` + `]` 组合键进入 telnet 命令行。

## 示例

```
airvip@airvip:~$ telnet 127.0.0.1 7272
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
hello 7f00000108fc00000001
7f00000108fc00000001 login
^]

telnet> quit
Connection closed.
airvip@airvip:~$
```

## 常用 telnet 命令

| 命令 | 解释 |
|----|----|
|? | 显示帮助信息 |
|close | 关闭当前连接 |
|logout | 强制退出远程用户并关闭连接 |
|display | 显示当前操作的参数|
|mode | 试图进入命令行方式或字符方式|
|open | 连接到某一站点|
|quit | 退出|
|send | 发送特殊字符，send ? 更过帮助|
|set | 设置当前操作的参数，set ? 更多帮助|
|unset | 复位当前操作参数 |
|status | 打印状态信息 |
|toggle | 对操作参数进行开关转换 |
|slc | 改变特殊字符的状态 |
|auth |打开/关闭确认功能|
|z |挂起 |
|telnetenviron |更改环境变量 |







