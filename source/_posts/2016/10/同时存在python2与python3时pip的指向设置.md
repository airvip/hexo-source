---
title: 同时存在python2与python3是pip的指向设置
date: 2016-10-23 00:09:55
tags:
    - Python
    - Pip
---

> 真正的感情,是要两个人能聊到一起的;最好的情侣,是能用灵魂平等地交流,是能用心去温暖彼此的两个人。

Python3 是大势所趋，然而还有很多项目是跑的 Python2，我们在安装包依赖时使用 Pip,就涉及到是安装的 Python3 的依赖，还是 Python2 的依赖。为了使用我们常用的 Python 版本，如何配置 Pip 直接下载我们需要的 Python 版本的依赖。 

<!-- more -->

现在的 Python3 与 Python2 是不兼容的，所以很多人电脑上都装了两个 Python 版本。像流行的 Ubuntu 16.04 就直接带了 Python3 与 Python2。


# 安装pip

由于在 Ubuntu 16.04 自带了 Python3 与 Python2，我们要费别为其安装 pip

``` Bash
$ sudo apt-get install python3-pip # for python3
$ sudo apt-get install python-pip # for python2
```

查看 pip 使用的 Python 版本

``` Bash
$ pip -V
pip 8.1.2 form /usr/local/lib/python2.7/dist-packages/pip (python 2.7)
```

可以看到 pip 使用的是 Python 2.7, 之后开发肯定是以 Python 3 为主，为了方便开发时安装依赖，我们稍微修改下，当然,不做修改我们可以直接使用 `pip3 install` 来安装。

# 修改pip指向Python3

查看pip位置

``` Bash
$ which pip
/usr/local/bin/pip
```

直接进行修改 ` sudo vim /usr/local/bin/pip`,看到如下内容

``` Bash
#!/usr/bin/python

# -*- coding: utf-8 -*-
import re
import sys

from pip._internal import main

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```

修改内容如下
`#!/usr/bin/python` 修改成 `#!/usr/bin/python3.5` 
`from pip._internal import main` 修改成 `from pip import main`

验证一下

```
$ pip -V
pip 8.1.2 form /usr/local/lib/python3.5/dist-packages (python 3.5)
```








