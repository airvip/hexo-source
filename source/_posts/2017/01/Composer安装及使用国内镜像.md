---
title: Composer安装及使用国内镜像
date: 2017-01-19 23:30:42
tags:
   - Composer
---

> 人生中有些事你不竭尽所能去做，你永远不知道自己有多出色！

Composer 是 PHP 的一个依赖管理工具。它允许你申明项目所依赖的代码库，它会在你的项目中为你安装他们。

<!-- more -->

# 依赖管理

Composer 不是一个包管理器。是的，它涉及 "packages" 和 "libraries"，但它在每个项目的基础上进行管理，在你项目的某个目录中（例如 vendor）进行安装。默认情况下它不会在全局安装任何东西。因此，这仅仅是一个依赖管理。
这种想法并不新鲜，Composer 受到了 node's npm 和 ruby's bundler 的强烈启发。而当时 PHP 下并没有类似的工具。

# 全局安装（推荐）

全局安装是将 Composer 安装到系统环境变量 PATH 所包含的路径下面，然后就能够在命令行窗口中直接执行 composer 命令了。

在 Linux 和 Mac OS X 中可以运行如下命令：
1. 下载composer  : 
``` bash
$ curl -sS https://getcomposer.org/installer | php
```

2. 将前面下载的 composer.phar 文件移动到 `/usr/local/bin/` 目录下面
``` bash
$ sudo mv composer.phar /usr/local/bin/composer
```
       
在windows安装
1. 下载并运行 [Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe)。


# 使用国内镜像

方法一：修改 composer 的全局配置文件（推荐）

打开命令行窗口（windows用户）或控制台（Linux、Mac 用户）并执行如下命令

``` bash
$ composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

方法二： 修改当前项目的 composer.json 配置文件

打开命令行窗口（windows用户）或控制台（Linux、Mac 用户），进入你的项目的根目录（也就是 composer.json 文件所在目录），执行如下命令

``` bash
$ composer config repo.packagist composer https://packagist.phpcomposer.com
```

手动修改

查看 composer 主目录：通过 composer config -l -g 命令可以列出 composer 的全局配置信息，在这些信息中查找 [home] 配置项就是 composer 的主目录。Linux 用户请执行 sudo composer config -l -g 命令。

进入 composer 的主目录，打开 config.json 文件（如果没有就自己创建一个），然后增加本镜像服务的配置信息，最终内容如下

``` json
{
    "config": {},
    "repositories": {
        "packagist": {
            "type": "composer",
            "url": "https://packagist.phpcomposer.com"
        }
    }
}
```