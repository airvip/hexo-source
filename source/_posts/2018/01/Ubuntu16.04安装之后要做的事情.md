---
title: Ubuntu16.04安装之后要做的事情
date: 2018-01-28 23:22:37
tags:
    - Ubuntu
---

> 贫乳是社会地位的象征。是具有稀有价值的！

不说了，最近把自己的老旧笔记本更换成 windows7 + Ubuntu 16.04 双系统了，新装的 Ubuntu 系统少不了一顿折腾，不要的软件清理掉，该装的应用要装上。

<!-- more -->

系统中带了一堆我们用不到的软件，Linux 系统的好处就是系统是你自己的，想怎么配置看自己心情。

## 删除操作

1. 删除Amazon的链接
``` bash
$ sudo apt-get remove unity-webapps-common
```

2. 删除libreoffice
libreoffice 虽然是开源的，但是是用 Java 写出来的，效率可想而知，请根据自身情况选择是否删除，可以用 wps 替代。
``` bash
$ sudo apt-get remove libreoffice-common
```

3. 删除掉不常用的自带软件
``` bash
$ sudo apt-get remove thunderbird totem rhythmbox simple-scan gnome-mahjongg aisleriot gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku onboard deja-dup
```
|软件名|软件包名|用途 |
|----|----|----|
| 雷鸟邮件 | thunderbird | 收发邮件 |
| totem播放器 | totem | 视频播放器 |
| Rhythmbox 音乐播放器 | rhythmbox | 音频播放器 | 
| empathy | empathy | 即时通讯工具 |
| brasero 光盘刻录器 | brasero | 光盘刻录 |
| 扫描易 | simple-scan | simple-scan 扫描工具 |
| 对对碰 | gnome-mahjongg | 娱乐游戏 |
| aisleriot 接龙游戏 | aisleriot | 娱乐游戏 |
| 扫雷 | gnome-mines | 娱乐游戏 |
| 茄子大头贴 | cheese | 拍照视频 |
| Transmission BitTorrent 客户端| transmission-common | 种子下载器 |
| 屏幕阅读 | gnome-orca | 阅读 |
| 浏览器 | webbrowser-app  | 上网 |
| 数独 | gnome-sudoku | 娱乐游戏 |
| 管理服务 | landscape-client-ui-install  |  管理服务 |
| 虚拟键盘 | onboard  | 虚拟键盘 |
| 备份 | deja-dup | 备份 |

可选删除
``` bash
$ sudo apt-remove -y remove yelp # 帮助
$ sudo apt-remove -y remove bluez # 蓝牙
$ sudo apt-remove -y remove gnome-software # Ubuntu 软件（软件商城）
$ sudo apt-remove -y remove unity # unity 桌面 可以更换为 gnome 桌面
$ sudo apt-remove -y remove gnome-system-monitor # 系统监视器
$ sudo apt-remove -y remove gnome-system-log # 系统日志
```

这样我们的 Ubuntu 系统基本已经干净了。

## 安装操作

1. 安装Vim
``` bash
$ sudo apt-get install vim
```

2. 安装 unrar
``` bash
$ sudo apt-get -y install unrar
```

3. 安装搜狗输入法
``` bash
$ vim /etc/apt/sources.list.d/ubuntukylin.list # 新建Ubuntukylin 软件源文件
```
  写入下面代码，并保存
`deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main`
再执行下面代码
``` bash
$ sudo apt-get update  # 更新软件源
$ sudo apt-get install sogoupinyin  # 安装搜狗输入法
```

4. 安装谷歌浏览器
在`https://dl.google.com/Linux/direct/google-chrome-stable_current_amd64.deb`下载deb安装包，在依次执行
``` bash
$ sudo apt-get install libappindicator1 libindicator7  
$ sudo dpkg -i google-chrome-stable_current_amd64.deb   
$ sudo apt-get -f install 
```

5. 安装 sublime text3
``` bash
$ 
```




