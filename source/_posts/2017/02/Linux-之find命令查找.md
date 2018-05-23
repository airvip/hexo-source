---
title: Linux 之find命令查找
date: 2017-02-27 12:10:15
tags:
    - Linux
---

> 没有什么是一个滑稽解决不了的，如果有，那就两个！

在对 Linux 服务器进行管理的时候，由于是在命令行窗口下进行的操作，有些文件久了就忘记被自己丢在哪了，所以，在 linux 下能够进行快速查找显得尤为重要，今天我们就来细细聊下 Linux 中的 `find` 命令。

<!-- more -->

Linux find命令用来在指定目录下查找文件。

# find语法

``` 
# find [搜索路径] [参数] [表达式] [执行操作]
find [path] [option] [expression] [-exec -ok command] {} \;
```
find 根据下列规则判断 path 和 expression，在命令列上第一个 `-`或者`()`或者`!` 之前的部份为 path，之后的是 expression。

说明：
* 如果 path 是空字串则认为是当前路径；
* 如果 expression 是空字串则使用 -print 即将查找到的文件输出到标准输出。 
* `-exec command {} \;` 表示将查到的文件执行command操作,`{}`与`\;`之间有空格
* `-ok` 和 `-exec` 用法一致，只不过在操作前要询用户

# option选项
| 选项 |  说明  |
|:---- | :---- |
| -amin n | 在过去 n 分钟内被读取过的文件或目录，单位以分钟计算 |
| -atime -n +n | 按文件访问时间来查找文件，-n指n天以内，+n指n天以前 | 
| -cmin n | 在过去 n 分钟内被更改过的文件或目录 |
| -ctime -n +n  | 按文件创建时间来查找文件，-n指n天以内，+n指n天以前 | 
| -depth  | 从指定目录下最深层的子目录开始查找 |
| -follow  | 如果遇到符号链接文件，就跟踪链接所指的文件 |
| -fstype 文件系统类型 | 查指定文件系统类型的文件，文件系统类型可 在/etc/fstab中找到 |
| -gid n | 查找符合指定组编码的文件或目录 |
| -group 组名称 | 查找符合指定组名称的文件或目录 |
| -iname filename | 同 -name,但忽略字符大小写的差别 |
| -mmin n | 在过去 n 分钟内被更改过的文件或目录 |
| -mount   | 查文件时不跨越文件系统mount点 |
| -mtime -n +n | 按文件更改时间来查找文件，-n指n天以内，+n指n天以前 | 
| -name filename | 查找名为filename的文件 |
| -newer f1 !f2 | 查更改时间比f1新但比f2旧的文件 |
| -nogroup| 查无有效属组的文件，即文件的属组在/etc/groups中不存在 | 
| -nouser | 查无有效属主的文件，即文件的属主在/etc/passwd中不存 | 
| -path  | 指定字符串作为寻找目录的范本样式 |
| -perm 777 | 按执行权限来查找 |
| -prune | 忽略某个目录 |
| -regex "正则匹配表达式"| 指定字符串作为寻找文件或目录的范本样式 |
| -size n |  查找符合指定的文件大小的文件 |
| -type b/d/c/p/l/f | 查是块设备、目录、字符设备、管道、符号链接、普通文件 |
| -user 用户名 |按文件属主来查找 |
| -version 或 ——version | 显示版本信息 |


# 基本使用

列出当前目录及自门路下所有的文件与文件夹
``` bash
$ find
```

列出当前用户的 home 目录中以 .txt 结尾的文件
``` bash
$ find ~ -name "*.txt"
```

列出当前用户的 home 目录中不是以 .txt 结尾的文件
``` bash
$ find ~ ! -name "*.txt"
```

列出/log目录下以数字开头的文件
``` bash
$ find /log -name "[0-9]*" -print
```

列出当前目录及子目录下查找所有以.txt和.pdf结尾的文件
``` bash
$ find . \( -name "*.txt" -o -name "*.pdf" \)
$ find . -name "*.txt" -o -name "*.pdf" 
$ find . -regex ".*\(\.txt\|\.pdf\)$"
```

列出 /usr 目录下包含 local 的文件或路径
``` bash
$ find /usr/ -path "*local*"
```

# 按文件类型查
``` bash
$ find . -type 类型参数
```
类型参数列表：
* b —— 块设备
* c —— 字符设备
* d —— 目录
* f —— 普通文件
* l —— 符号连接
* p —— Fifo
* s —— 套接字

# 按文件大小查
``` bash
$ find . -type f -size 文件大小单元
```
文件大小单元:
* b —— 块（512字节）
* c —— 字节
* w —— 字（2字节）
* k —— 千字节
* M —— 兆字节
* G —— 吉字节

列出等于10KB的文件
```
$ find . -type f -size 10k
```
列出大于10KB的文件
```
$ find . -type f -size +10k
```
列出小于10KB的文件
```
$ find . -type f -size -10k
```

# 结合操作命令

找出当前目录下所有air的文件，并把所有权更改为用户yaya
``` bash
# {} 用于与-exec选项结合使用来匹配所有文件，然后会被替换为相应的文件名。
$ find .-type f -user air -exec chown yaya {} \;
```

找出自己家目录下所有的.txt文件并删除
``` bash
$ find $HOME/. -name "*.txt" -ok rm {} \;
```

查找当前目录下所有.txt文件并把他们拼接起来写入到all.txt文件中
``` bash
$ find . -type f -name "*.txt" -exec cat {} \;> all.txt
```

将30天前的.log文件移动到old目录中
``` bash
$ find . -type f -mtime +30 -name "*.log" -exec mv {} old \;
```

找出当前目录下所有.txt文件并以“Name:文件名”的形式打印出来
``` bash
$ find . -type f -name "*.txt" -exec printf "Name: %s\n" {} \;
```

# 其他
查找当前目录或者子目录下所有.txt文件，但是跳过子目录dev
``` bash
$ find . -path "./dev" -prune -o -name "*.txt" -print
```

要列出所有长度为零的文件
``` bash
$ find . -empty
```

由于单行命令中-exec参数中无法使用多个命令，我们可以先把命令写入脚本，再通过-exec执行脚本达到执行多条命令的需求
``` bash
$ find . -name "*.txt" -exec ./text.sh {} \;
```






