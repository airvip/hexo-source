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

# 删除操作

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
$ sudo apt-get -y remove yelp # 帮助
$ sudo apt-get -y remove bluez # 蓝牙
$ sudo apt-get -y remove gnome-software # Ubuntu 软件（软件商城）
$ sudo apt-get -y remove unity # unity 桌面 可以更换为 gnome 桌面
$ sudo apt-get -y remove gnome-system-monitor # 系统监视器
$ sudo apt-get -y remove gnome-system-log # 系统日志
```

这样我们的 Ubuntu 系统基本已经干净了。

# 安装操作

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
$ sudo add-apt-repository ppa:webupd8team/sublime-text-3    
$ sudo apt-get update    
$ sudo apt-get -y install sublime-text   
```

6. 安装经典菜单指示器
``` bash
$ sudo add-apt-repository ppa:diesch/testing  
$ sudo apt-get update  
$ sudo apt-get install classicmenu-indicator  
```
  ![classicmenu-indicator](/img/201801/ubuntu1604/classicmenu.png)

7. 安装系统指示器SysPeek
``` bash
$ sudo add-apt-repository ppa:nilarimogard/webupd8    
$ sudo apt-get update    
$ sudo apt-get install syspeek  
```
  ![syspeek](/img/201801/ubuntu1604/syspeek.png)

8. 安装vpnc和git
``` bash
$ sudo apt-get install vpnc git
```

9. 安装nodejs和npm
``` bash
$ sudo apt-get -y install nodejs npm nodejs-legacy
$ npm config set registery https://registry.npm.tabao.org # 使用淘宝镜像
$ npm config list # 查看npm配置信息
$ sudo npm install n -g # 全局安装n管理器(用于管理nodejs版本)
$ sudo n stable # 安装最新nodejs 稳定版
$ sudo node -v # 查看版本
```


10. 安装vscode
  去`https://code.visualstudio.com/`下载deb安装包
``` bash
$ sudo dpkg -i <file>.deb
$ sudo apt-get install -f
```

11. 安装多线程下载神器 axel
``` bash
$ sudo apt-get -y install axel
```

12. 安装apt-fast
  apt-fast 是用 axel 来加速 apt-get 软件安装的脚本,由于是多线程下载，所以加速效果还是很明显的。
  使用 apt-fast 命令替代原 apt-get 命令即可。
``` bash
$ sudo apt-get -y install apt-fast
```

13. 安装openssh-server
``` bash
$ sudo apt-get -y install openssh-server 
```

14. 安装lnav
lnav工具是在终端界面看日志的神器
``` bash
$ sudo apt-get -y install lnav
$ lnav /var/log/dpkg.log # 查看dpkg日志
```

15. 自定义DHCP网络的DNS Server IP地址
  执行 `sudo vim /etc/dhcp/dhclient.conf` ,在 `#prepend domain-name-servers 127.0.0.1;` 下面添加114与阿里云的 DNS，这样会优先使用 aliyun 的 DNS，次要使用 114 的 DNS
``` bash
#prepend domain-name-servers 127.0.0.1;
prepend domain-name-servers 114.114.114.114;  
prepend domain-name-servers 223.5.5.5;  
```

16. 安装python包管理器pip
``` bash
$ sudo apt-get install python3-pip # for python3
$ sudo apt-get install python-pip # for python2
```

17. 安装shutter截屏软件
``` bash
$ sudo apt-get -y install shutter
```

18. 安装curl
``` bash
$ sudo apt-get -y install curl
```

19. 安装composer,php的管理包
``` bash
$ curl -sS https://getcomposer.org/installer | php
$ sudo mv composer.phar /usr/local/bin/composer
# 安装完composer先使用国内镜像
$ composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

20. 安装redis数据库
```
$ sudo apt-get -y install redis-server
$ redis-cli # 进入redis
> config set requirepass 123456 # 设置密码
```





