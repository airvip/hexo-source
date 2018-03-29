---
title: Ubuntu16.04安装微信(wechat)
date: 2018-02-07 01:07:37
tags:
    - Ubuntu
    - Wechat
---

> 没问题，绝对没问题。

现在微信 8 亿的用户群，导致大家在平时工作中生活中沟通已经完全离不开微信，所以在 Ubuntu 中安装微信也是必不可少的。

<!-- more -->


## electronic-wechat

由于鹅厂一贯的作风，电脑端软件只针对 Windows 与 Mac 做开发，冷落了我们的大 Linux，于是社区发布了 Electronic WeChat 开源项目，这是一个社区发布的产品, 而不是官方微信团队发布的产品。


[electronic-wechat 源码在 GitHub 上托管的地址](https://github.com/geeeeeeeeek/electronic-wechat)
[electronic-wechat 二进制包在 GitHub 上托管的地址](https://github.com/geeeeeeeeek/electronic-wechat/releases)

electronic-wechat 微信客户端基于 Electronic 技术开发。
electronic-wechat 微信客户端到目前为止已升级 Electron 至 V1.4.15，Chromium 至 54。
Electron 提供了丰富的本地(操作系统)的 API ,使你能够使用纯 JavaScript 来创建桌面应用程序, 支持 Mac, Linux, Windows 可谓是全平台, 项目使用 MIT 许可。


## 安装 & 使用

1. 源码编译安装

需要依赖 git node npm,如果电脑上没有请提前安装，可以查看 《Ubuntu16.04安装之后要做的事情》 这篇博客。

下载仓库
``` bash
$ git clone https://github.com/geeeeeeeeek/electronic-wechat.git
```

进入仓库
``` bash
$ cd electronic-wechat
```

安装依赖, 运行应用
``` bash
$ npm install && npm start
```

根据自己的系统平台运行下面的命令进行打包
``` bash
$ npm run build:osx
$ npm run build:linux
$ npm run build:win32
$ npm run build:win64
```

2. 使用二进制包安装

下载最新的发行包
``` bash
$ wget https://github.com/geeeeeeeeek/electronic-wechat/releases/download/V2.0/linux-x64.tar.gz
```

解压
``` bash
$ tar -zxvf linux-x64.tar.gz
```

然后把应用程序包移动到 /opt 下
``` bash
$ sudo mv electronic-wechat-linux-x64/ /opt/
```

下载图标
``` bash
$ wget https://raw.gercontent.com/geeeeeeeeek/electronic-wechat/master/assets/icon.png -O /opt/electronic-wechat-linux-x64/icon.png
```

创建一个微信电脑版的桌面启动器
``` bash
$ sudo vim /usr/share/applications/electronic-wechat.desktop
```
写入下面内容
``` bash
[Desktop Entry]
Name=Electronic WeChat
Name[zh_CN]=微信
Name[zh_TW]=微信
GenericName=Electronic wechat
Icon=/opt/electronic-wechat-linux-x64/icon.png
Exec=/opt/electronic-wechat-linux-x64/electronic-wechat
Encoding=UTF-8
Version=v1.4.15
Type=Application
Terminal=false
X-MultipleArgs=false
StartupNotify=false
StartupWMClass=electronic-wechat
OnlyShowIn=Unity;X-UnityGenerated=true
Categories=Application;Utility;Network;InstantMessaging;
```

我的 `electronic-wechat` 应用程序包放在了`/opt/` 目录下, 而启动器对应 `/usr/share/applications` (全局有效)
应用程序包如果放在自己的 `$HOME(~)` 下面, 而启动器则放到`~/.local/share/applications` 目录下 (本用户有效)


