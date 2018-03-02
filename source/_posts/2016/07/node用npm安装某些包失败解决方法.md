---
title: node用npm安装某些包失败解决方法
date: 2016-07-25 17:30:09
tags:
    - Npm
    - Node
---

> Just give me that chance.

作为中国当下的一名 coder,奈何一道厚厚的墙阻止了我们很多正常工作的便利，所以我们不得不学会的一项技能就是翻过高墙看看世界的娃娃们都在干什么？扯远了，现在 npm 安装包出错，直接解决。

<!-- more -->

推荐大家使用[淘宝npm镜像](https://npm.taobao.org/)

## 配置方法

方法一：通过config命令配置
``` bash
[root@localhost workspace]# npm config set registery https://registry.npm.tabao.org
[root@localhost workspace]# npm info underscore
{ name: 'underscore',
  description: 'JavaScript\'s functional programming helper library.',
  'dist-tags': { latest: '1.8.3', stable: '1.8.3' },
  versions: 
   [ '1.0.3',
     '1.0.4',
...............后面的文档省略................
```

方法二：命令行指定配置
``` bash
[root@localhost workspace]#npm --registry https://registry.npm.taobao.org info underscore
```

方法三：编辑 ~/.npmrc 加入下面内容
``` bash
[root@localhost ~]# ls -a
.    .bash_profile  .cshrc    mynote    .npmrc    .rediscli_history
[root@localhost workspace]# vi ~/.npmrc
registery=https://registry.npm.tabao.org
```
使用哪种方法可根据自己需要以及爱好

## cnpm 替换npm

淘宝定制了cnpm命令行工具来替代原生npm
``` bash
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```
或者你直接通过添加 npm 参数 alias 一个新命令
``` bash
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"

# Or alias it in .bashrc or .zshrc
$ echo '\n#alias for cnpm\nalias cnpm="npm --registry=https://registry.npm.taobao.org \
  --cache=$HOME/.npm/.cache/cnpm \
  --disturl=https://npm.taobao.org/dist \
  --userconfig=$HOME/.cnpmrc"' >> ~/.zshrc && source ~/.zshrc
```

使用 cnpm 安装模块
``` bash
$ cnpm install [package name]
```

更多的cnpm使用传送门：[cnpm使用方法](https://npm.taobao.org/)