---
title: Python实现微信聊天机器人
date: 2017-04-01 10:10:05
tags:
    - Python
---

> 梦总是有会醒来的时候，不会醒的梦总有一天会变成悲伤。

有时候自己对于微信中的消息懒得回复应该怎么办？我们使用聊天机器人就很好解决了，只需要登录自己的微信账号，只要有关于自己的消息，我们一概可以使用智障机器人做处理。

<!-- more -->

使用前提：
1. 已经安装 Python
2. 了解 Python 基础
3. 有微信账号
4. 已经申请图灵机器人账号

重要说明：
由于机器人现在还比较智障，该案例只用于测试，不能用于生产环境。
使用机器人存在一定概率被限制登录的可能性,主要表现为无法登陆 Web 微信 (但不影响手机等其他平台)。

## 申请机器人

在 [图灵机器人官网](http://www.tuling123.com/) 注册，进入》机器人管理》点击创建机器人
![创建机器人](/img/201704/wxrobot/wxrobot01.png)

在创建的机器人中的，机器人设置中 获取 API地址 及 APIkey
![机器人key](/img/201704/wxrobot/wxrobot02.png)

## 安装 wxpy

wxpy 支持 Python 3.4-3.6，以及 2.7 版本
如果机器上安装了 Python2 ,又安装了 Python3,请将下方命令中的 “pip” 替换为 “pip3” 或 “pip2”，可确保安装到对应的 Python 版本中

1. 从 PYPI 官方源下载安装 (在国内可能比较慢或不稳定)
```
pip install -U wxpy
```

2. 从豆瓣 PYPI 镜像源下载安装 (推荐国内用户选用)
```
pip install -U wxpy -i "https://pypi.doubanio.com/simple/"
```

## 编程实现

```
#!/usr/bin/env python3
# -*- coding:utf-8 -*-
# filename wxrobot.py

import requests
from wxpy import *
import json

# 创建机器人对象，开启缓存后可在短时间内避免重复扫码，缓存失效时会重新要求登陆。
# 设为 True 时，使用默认的缓存路径 ‘wxpy.pkl’
bot = Bot(cache_path=True) 

def talks_robot(info = '在吗?'):
    api_url = 'http://www.tuling123.com/openapi/api' # 在 机器人设置中 中获取的 API地址
    apikey = '6b1***5969ed'  # 在 机器人设置中 中获取的 apikey
    data = {'key': apikey, 'info': info}
    req = requests.post(api_url, data=data).text
    replys = json.loads(req)['text']
    return replys

@bot.register()
def reply_my_friend(msg):
    message = '{}'.format(msg.text)
    replys = talks_robot(info=message)
    # 如果是群聊，但没有被 @，则不回复
    if isinstance(msg.chat, Group) and not msg.is_at:
        return
    else:
        # 回复消息内容和类型
        return replys

bot.start() # 启动机器人
embed() # 进入 Python 命令行，方便测试其他功能
```

**运行操作**

在初始化时便会执行登陆操作，需要手机扫描登陆。

**运行结果**
```
./wxrobot.py
Getting uuid of QR code.
Downloading QR code.
Please scan the QR code to log in.
Please press confirm on your phone.
Loading the contact, this may take a little while.
Login successfully as 阿尔あ维奇จุ๊บ

>>> bot.friends()
[<Friend: 阿尔あ维奇จุ๊บ>, <Friend: 陈实>] # 好友列表
```

使用另一个账号给 阿尔あ维奇จุ๊บ 测试

![真机测试聊天](/img/201704/wxrobot/wxrobot03.png)