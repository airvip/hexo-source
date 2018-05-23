---
title: 在github上搭建自己的静态网站并配置域名
date: 2016-08-04 00:50:48
tags:
    - GitHub Pages 
---

> 成熟不是人的心变老，是泪在打转还能微笑。

github 不仅是全球码农交友的最大平台，其实还是好多电子书首选托管的舞台，当然 github 也允许广大开发者或机构搭建自己的 pages,今天我们就简单做个 demo 玩一把。

<!-- more -->

**提醒** 在 github 上搭建的网站是面向全网络公开的，即便你是付费用户创建的私有仓库。如果在你的私有仓库里面存在敏感数据，请在发布网站前移除掉。

其实在github上搭建网站十分简单，如果你的英文水平不错可以直接查看官方文档，当然如果你像我一样英文有点薄弱，可以按下面的步骤一步步搭建自己的静态网页（如：个人介绍、个人博客网站），你也可以使用github提供的模版，快速生成网站，当然在这里我将以自我定制开发为主来搭建站点。

[github pages 搭建静态站点的步骤](https://pages.github.com/)

# 创建项目

1、首先你要有一个 github 帐号，没有的还不快去注册一个。（连个 github 帐号都没有，还好意思做开发...）

2、拥有了 github 帐号，就来创建一个自己的代码仓库吧。

注意：在给创建的仓库命名时，一定要写成   (你的名称.github.io)，这里的你的名称就是你在github上的用户名(组织名)

贴上一段github上给的说明

> If the part of the repository doesn’t exactly match your username, it won’t work, so make sure to get it right.
> 如果你的仓库名这部分命名与你的用户名不匹配，你的网站将不能正常运行，所以请你确保此处无误。

3、然后就是使用（命令终端、win客户端、mac客户端）迁出你的项目，进行编码操作。当然咱们的index.html 是必不可少的，所以，先把 index.html 文件弄好。

4、下面的步骤就和开发正常的网址一样了，添加到 github，提交到 github，推送到 github。

![git clone](/img/201608/githubpages/gitclone.png)

# 域名绑定

接下来咱们绑定自己的域名，当然不绑定也可以，你直接访问(你的名称.github.io)就可一看到效果了。

1、进入你的仓库，切换到setting选项卡，在里面找到Github pages面板，在github pages面板里面有一项是custom domain,填写上你的域名，如下
![respositories](/img/201608/githubpages/respositories.png)

2、去你的域名服务提供商那里绑定域名，我的是万网，绑定如下，绑定页面其实都差不多
![binddomain](/img/201608/githubpages/binddomain.png)