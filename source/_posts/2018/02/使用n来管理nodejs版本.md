---
title: 使用n来管理nodejs版本
date: 2018-02-13 09:05:22
tags: 
    - Node
---

> 舞台上演员不能无视剧本随便演，华丽地退场才是完成使命。

n 是 Node 的一个模块，作者是TJ Holowaychuk（鼎鼎大名的 Express 框架作者），就像它的名字一样，它的理念就是简单。

<!-- more -->

# n 模块安装

``` bash
$ npm install n -g  # 全局安装n管理器
```

安装完成之后，在 terminal 中直接输入 `n` 后输出当前已经安装的 node 版本以及正在使用的版本（前面有一个o），你可以通过移动上下方向键来选择要使用的版本，最后按回车生效，如果使用 n 更改版本没有权限，请使用 `sudo n`。

``` bash
n
  ο node/4.1.0
    node/6.10.0
    node/9.3.0
    node/9.10.1
```

## 帮助命令
``` bash
$ n --help
$ n -h
```

## 查看node版本
``` bash
$ n list
$ n ls
```

## 安装node其他版本
例如安装node 6.10.0,记住安装完之后 node 默认切换到该版本
  ```
  $ sudo n 6.10.0
      install : node-v6.10.0
           mkdir : /usr/local/n/versions/node/6.10.0
           fetch : https://nodejs.org/dist/v6.10.0/node-v6.10.0-linux-x64.tar.gz
        installed : v6.10.0
  $ n latest # 安装node最新版本
  $ n stable # 安装node最新稳定版
  $ n lts # 安装node最新长支持发行版本
  ```

## 删除node某个版本
``` bash
$ n rm 4.1.0
```

## 制定版本运行脚本
``` bash
$ n use 6.10.0 test.js
```

## 查看n版本
``` bash
$ n -V
$ n --version
```

