---
title: 多终端PC同步管理 hexo 博客
date: 2017-02-12 23:46:27
tags:
    - Hexo
    - Git
---

> 人总是会变的，不要害怕改变哦。

当我们创建了 hexo 博客之后，我们一般都会有在多终端同时写作的需求，我们就可以使用 git 来管理我们博客源码，做到多终端同步管理的效果。

<!-- more -->

## 需求原因

我们在家里面有台笔记本并搭建了 hexo 博客，在公司要使用公司的台式机来工作，又想在公司休闲的时候写写博客，所以就需要这两台电脑能够同时对博客进行管理发布。

再多终端 PC 进行博客管理，还可以达到对博客源码进行备份的效果。所有感觉多终端管理博客还是非常重要的。

## 需求解决

电脑A：家中电脑，搭建了 hexo 博客
电脑B：公司电脑，实现可以像家中电脑一样对博客进行管理

默认大家已经在自己的电脑上都已经安装并配置好了： git 、node 、npm。 

1. 看检查 电脑A 的博客项目根目录下是否有 .gitignore,没有请自行创建，.gitignore 内容如下
``` 
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```
2. 在 github 上建立源码仓库，如：hexo-source
![创建源码仓库](/img/201702/hexosource/create_repository.png)
3. 在 电脑A 上将源码托管到 github 的 hexo-source 仓库
说明：之前在 github 上配置了 ssh 的公钥，可以使用 ssh 链接，若没有，请使用 https 链接。
如果，博客项目根目录下面已经有了 .git 隐藏目录，在 gitbash 下执行下面代码
``` bash
$ git remote add origin git@github.com:airvip/hexo-source.git #添加远程仓库
$ git add .
$ git commit -m "create hexo-source"
$ git push -u origin master
```
  如果，博客项目根目录下面没有 .git 隐藏目录，在 gitbash 下执行下面代码
``` bash
$ git init #初始化 git
$ git remote add origin git@github.com:airvip/hexo-source.git #添加远程仓库
$ git add .
$ git commit -m "create hexo-source"
$ git push -u origin master
```
4. 在 电脑B 上 clone 我们的 hexo-source 仓库,在 gitbash 下执行
``` bash
$ git clone git@github.com:airvip/hexo-source.git
```

5. 在 电脑B 上进行安装依赖,在 gitbash 下执行
``` bash
$ npm install
```

现在就可以在两台电脑上对源码进行编辑提交了。
这是对 hexo 博客的源码进行托管处理。

## 模拟操作

先在 电脑A 编写文章
``` bash
#新建文章
$ hexo new "电脑A文章" 
#编写完发布源码
$ git add .
$ git commit -m "电脑A文章"
$ git push origin master
#编译后发布博客
$ hexo g
$ hexo d
```

再在 电脑B 上创作管理文章

``` bash
#拉取源码
$ git pull origin master
#新建文章
$ hexo new "电脑B文章" 
#编写完发布源码
$ git add .
$ git commit -m "电脑B文章"
$ git push origin master
#编译后发布博客
$ hexo g
$ hexo d
```

这样就可以在多终端 PC 上对 hexo 博客进行管理操作了。





