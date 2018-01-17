---
title: Hexo更换主题与给文章加标签
date: 2017-01-10 22:44:44
tags:
    - Hexo
    - 静态博客
---

> 人靠衣装，博客靠靓装。

这篇博客主要记录两个事情：1.如何给自己的hexo静态博客穿上漂亮的衣服。2.新建了hexo静态博客如何添加多个标签。

<!-- more -->

## 更换主题

前提条件：已经安装 git

因为博主是个伪前端，还是一个拿来主义受益者，所以也只能去 github 上看看有哪家的东西比较好，就直接使用。记着一定要保留原作者的一些痕迹，当做是对他们的敬意。

### 寻找目标
在 github 中全局搜索 `hexo-theme` 关键词，筛选自己心仪的主题对象，确定好之后，进入自己博客的主目录，右键点击 Git Bash Here ,输入以下命令。
``` bash
git clone https://github.com/litten/hexo-theme-yilia.git themes/hexo-theme-yilia
```
或者在自己博客的 `themes` 目录下，右键点击 Git Bash Here ,输入以下命令。
``` bash
git clone https://github.com/litten/hexo-theme-yilia.git
```

### 启用主题
修改自己博客主目录中的 `_config.yml` 中的 theme 属性，修改如下：
``` bash
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
#theme: landscape
theme: hexo-theme-yilia
```

## 文章添加标签
给文章添加标签就相当于对文章进行了标签化分类，方便之后自己温故而知新，即便很少来温故，但是做标签化分类还是一个很好的习惯的。

### 新建文章
先新建一篇文章，代码如下：
``` bash
hexo new "Hexo设置标签"
```

### 编辑修改
编辑新建的 `Hexo设置标签.md` 文章
``` html
---
title: Hexo设置标签  #这是文章的标题
date: 2017-01-10 22:44:44 #文章生成的时间
categories: "Hexo教程" #文章分类目录 可以省略
tags:#文章标签 可以省略
    - Hexo
    - 静态博客
description: #文章的描述 利于SEO 可以省略
---
```
