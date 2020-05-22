---
title: 解决在 Linux 上执行 Windows下编写的 shell 报错
date: 2020-05-21 18:22:01
tags: 
    - Shell
    - Linux
---

> 作为烂泥，能不能不要扶我上墙！

在 Linux 上执行 Windows下编写的 shell 报 `-bash: ./server.sh: /bin/bash^M: bad interpreter: No such file or directory` 的错误

<!-- more -->

## 原因及分析

错误：`-bash: ./server.sh: /bin/bash^M: bad interpreter: No such file or directory`

分析：因为我们的 Shell 脚本是在 Windows 系统下编写的，所以可能存在不可见字符，从错误提示来看，极有可能脚本文件是 DOS 格式的，即：每一行的行尾以 \r\n 来标识, 其 ASCII 码分别是0x0D, 0x0A。

## 问题解决


1. 通过 `vim filename` 打开文件，此时处于命令行(底行)模式，如： `vim server.sh`
2. 直接在命令行模式下输入 `:set ff` 或者 `:set fileformat` 则看到文件的格式信息，如：`fileformat=dos`
3. 将 dos 文件格式修改为 unix，在命令行模式下输入 `:set ff=unix` 或者 `:set fileformat=unix`
4. 检查是否修改成功，在命令行模式下输入 `:set ff` 或者 `:set fileformat` 可以看到反馈信息为 `fileformat=unix` 即为修改成功。
5. 再次尝试运行 Shell 脚本，`./server.sh start` 可以看到正常运行。


## 原因及分析

错误：`./server.sh: line 31: [: missing ']'`

分析：提示脚本 31 行缺少了 `]`, 一般都是我们编写的 Xshell 脚本有问题，如：空格不对

## 问题解决

1. 通过 `vim filename` 打开文件，此时处于命令行(底行)模式，如： `vim server.sh`
2. 直接在命令行模式下输入 `:set nu`, 显示行号
3. 在第 31 行发现代码 `if [ ! -d "$LOG_DIR"];then`, 好吧！格式不正确，少了个空格 
4. 修改 31 行的代码如下 `if [ ! -d "$LOG_DIR" ];then`,保存。
5. 再次尝试运行 Shell 脚本，OK,正常运行了。


