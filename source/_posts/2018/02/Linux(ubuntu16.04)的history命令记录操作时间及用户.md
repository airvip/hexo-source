---
title: Linux(ubuntu16.04)的history命令记录操作时间及用户
date: 2018-02-21 00:30:20
tags:
    - Linux
    - Ubuntu
---

> 如果你执意追寻着我的幻影，总有一天会被真正的我打败。

history 这是我们在终端下操作 linux 服务器时经常使用的命令，有时候我们想将执行这些命令的用户及操作时间也记录下来方便大家查看，于是我们就要修改下记录命令的格式。

<!-- more -->

## history的介绍
history 时 shell 的内置命令，我们可以通过 man 命令快速查看其帮助文档。
history 是显示在终端下输入并执行的命令，系统默认保留 1000 条，如果你认为不够，也可以自行修改。
history 可以非常方便的帮助我们快速定位之前的某些操作。
系统在关闭后会将现有的 history 内容保存到文件 `～/.bash_history`

**与history相关的环境变量**


* HISTFILE : 用于设置命令历史的保存位置，默认保存在`~/.bash_history`文件中。
* HISTFILESIZE : 命令历史文件记录历史的条数 
* HISTSIZE : 命令历史记录的条数，默认为1000 
* HISTTIMEFORMAT="%F %T"  : 显示命令发生的时间 
* HISTIGNORE="str1:str2:..." 忽略string1,string2历史 
* HISTCONTROL :包含一下4项，让哪一项生效只需要让其为下面一项即可 
  1. ignoredups:  忽略重复的命令；连续且相同方为“重复” 
  2. ignorespace:  忽略所有以空白开头的命令 
  3. ignoreboth:ignoredups,ignorespace 的组合
  4. erasedups:    删除重复命令


## history记录用户及时间

如果只是针对本用户我们可以修改 `~/.bashrc` 在文件的末尾加上 `HISTTIMEFORMAT="`whoami` $F %T "` 这句话即可。
使其生效请执行`source ~/.bashrc`,这样免去重启电脑。
查看 history


针对本用户我们也可以修改 `~/.profile` 在文件的末尾加上 `export HISTTIMEFORMAT="`whoami` $F %T "` 这句话即可。
使其生效请执行`source ~/.bashrc`,这样免去重启电脑。


如果只是针对全局用户我们可以修改 `/etc/bash.bashrc` 在文件的末尾加上 `HISTTIMEFORMAT="`whoami` $F %T "` 这句话即可。
使其生效请执行`source /etc/bash.bashrc`，这样免去重启电脑。

针对本用户我们也可以修改 `/etc/profile` 在文件的末尾加上 `export HISTTIMEFORMAT="`whoami` $F %T "` 这句话即可。
使其生效请执行`source /etc/profile`，这样免去重启电脑。

## history的几个命令

* 查看本次会话执行的命令历史(内存数据)
``` bash
$ history
```

* 追加本次会话新执行的命令历史(内存数据)列表至历史文件
``` bash
$ history -a
```

* 清空命令历史(内存数据)
``` bash
$ history -c
```

* 删除历史(内存数据)中指定的命令，n 表示命令行号
``` bash
$ history -d n # n是数字
```

* 读历史文件(本地数据)中未读过的行到历史列表(内存数据)
``` bash
$ history -n
```

* 读历史文件(本地数据)附加到历史列表(内存数据)
``` bash
$ history -r
```

* 保存历史列表(内存数据)到指定的历史文件(本地数据)
··· bash
$ history -w
···

* 显示最近的 n 条命令(内存数据)
``` bash
$ history n # n是数字
```

* 再次执行第 n 行命令
``` bash
$ !n # n是数字
```






