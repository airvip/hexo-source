---
title: 使用Hexo搭建静态博客
date: 2017-01-03 00:32:32
tags: #文章标签就不再进行分类
    - Hexo
    - 静态博客
---
> 新的一年，新的开始，学习从未止步，本文章作为学习教程使用，方便之后查阅。

之前的动态博客不会再更新，有时间也会把某些内容迁移过来，该静态博客未来将会作为我的发泄主战场，所以喜欢的朋友可以常来光顾。阿尔维奇在这里给大家说声：新年快乐 ☺☺☺
<!-- more -->

# Hexo简介

快速、简洁且高效的博客框架。基于 Node.js ，支持 GitHub Flavored Markdown 的所有功能，甚至可以整合 Octopress 的大多数插件。拥有强大的插件系统，安装插件可以让 Hexo 支持 Jade, CoffeeScript。


[Hexo中文网站传送门](https://hexo.io/zh-cn/)

# 环境搭建

依赖环境: `node.js`、`npm(或cnpm)`，部署网站需要 `git`

## 安装

``` bash
# 全局安装 hexo-cli
$ npm install -g hexo-cli
# 待上面的命令执行结束，来查看安装信息
$ hexo -v 
```

## 建站

上面步骤完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

``` bash
# 初始化 hexo 站点，<folder> 表示文件夹
$ hexo init <folder>
# 进入初始化好的 hexo 站点目录
$ cd <folder>
# 安装依赖
$ npm install
```

## 网站体验

启动服务器。默认情况下，访问网址为： http://localhost:4000/。

``` bash
$ hexo server		
```



| 选项 | 描述 |
|:-----|:-----|
| -p, --port | 重设端口 |
| -s, --static | 只使用静态文件 |
| -l, --log | 启动日记记录，使用覆盖记录格式 |


## 其他命令

新建一篇文章.如果没有设置 layout 的话，默认使用 `_config.yml` 中的 `default_layout` 参数代替。如果标题包含空格的话，请使用引号括起来。
``` bash
$ hexo new [layout] <title>
```

生成静态文件。
``` bash
$ hexo generate
```

| 选项 | 描述 |
|:-----|:-----|
| -d, --deploy | 文件生成后立即部署网站 |
| -w, --watch | 监视文件变动 |

发表草稿。
``` bash
$ publish [layout] <filename>
```

部署网站。
``` bash
$ hexo deploy
```

| 选项 | 描述 |
|:-----|:-----|
| -g, --generate | 部署之前预先生成静态文件 |

清除缓存文件 (db.json) 和已生成的静态文件 (public)。
在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。
``` bash
$ hexo clean
```

[Hexo更多命令传送门](https://hexo.io/zh-cn/docs/commands.html)

## 命令简写
``` bash
$ hexo n # 等价于 hexo new
$ hexo g # 等价于 hexo generate
$ hexo s # 等价于 hexo server
$ hexo d # 等价于 hexo deploy
```

# 网站部署

编辑项目根目录下的 `_config,yml`

``` yml
# Deployment
# Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:airvip/airvip.github.io.git
  branch: master
```
.yml文件对格式规范要求很严格，type: repository: branch: 前面有两个空格，冒号后面都有一个空格。

编辑好之后就可以执行以下指令完成站点发布

```
$ hexo generate
$ hexo deploy
```

注意：

* 执行 `hexo deploy` 报了 `ERROR Deployer not found: git`，那说明你没有安装 hexo-deployer-git 依赖包，查看该站点 node_modules 发现真的没有 hexo-deployer-git，只需要输入下面命令安装 hexo-deployer-git 依赖包，然后再执行 `hexo deploy` 就能上传成功了.
``` bash
$ npm install hexo-deployer-git --save
```

说明：

* 需要了解如何部署 ssh 公钥
* 需要了解如何设置 github pages