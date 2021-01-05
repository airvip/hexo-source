---
title: 如何正确使用Composer安装扩展包
date: 2016-11-18 08:55:43
tags:
    - Composer
---

> "老板给我五杯昨天的酒" "是不是味道不错" "不是，因为同样的醉意我就可以再梦到她了"

对于 PHP 的项目，我们经常要往现有的项目中添加扩展包，我们通过 composer 可以非常方便快捷的添加扩展包，因为有时候对 composer 的某些命令理解不清晰，造成非常严重的后果。

<!-- more -->

安装扩展命令区分

* `composer install` - 如有 composer.lock 文件，直接安装，否则从 composer.json 安装最新扩展包和依赖；
* `composer update` - 从 composer.json 安装最新扩展包和依赖；
* `composer update firebase/php-jwt` - 从 composer.json 或者对应包的配置，并更新到最新；
* `composer require new/package` - 添加安装 new/package, 可以指定版本，如： `composer require firebase/php-jwt ~5.0`.

区分清楚了这几条命令，我们才可以轻松应对平时的开发。

# 几种流程分析

流程一：新项目

1. 创建 `composer.json`，并添加依赖到的扩展包；
2. 运行 `composer install`，安装扩展包并生成 `composer.lock`；
3. 提交 `composer.lock` 到代码版本控制器中,如：git;

流程二：项目协作者安装

克隆项目后，根目录下直接运行 `composer install` 从 `composer.lock` 中安装 指定版本 的扩展包以及其依赖；

此流程适用于生产环境代码的部署。

流程三：为项目添加新扩展包

1. 使用 `composer require firebase/php-jwt` 添加扩展包；
2. 提交更新后的 `composer.json` 和 `composer.lock` 到代码版本控制器中，如：git;

# composer.lock

composer.lock 文件里保存着对每一个代码依赖的版本记录，提交到版本控制器中，并配合 `composer install` 使用，保证了团队所有协作者开发环境、线上生产环境中运行的代码版本的一致性。

![composer.lock](https://s3.ax1x.com/2021/01/05/sF2y3q.jpg)


# 如何新增扩展包

准备添加一个扩展包，install, update, require 三个命令都可以用来安装扩展包，选择哪一个才是正确的呢？

强烈推荐使用 `composer require` 

另外，在手动修改 composer.json 添加扩展包后，`composer update new/package` 进行指定扩展包更新的方式，也可以正确的安装，不过不建议使用这种方法，因为，一旦忘记敲定后面的扩展包名，这样的坑不好填啊！
