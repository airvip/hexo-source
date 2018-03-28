---
title: ubuntu16.04解决You are using pip version 8.1.1, however version 9.0.3 is available.
date: 2018-03-25 15:20:56
tags: 
    - Python
    - Pip
    - Ubuntu
---

> 禁忌算什么！那难道比爱丽丝的命还重要吗？

You are using pip version 8.1.1, however version 9.0.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.

<!-- more -->

## 快速解决

``` bash
# 
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python3.5 get-pip.py # pip3
Collecting pip
  Downloading pip-9.0.3-py2.py3-none-any.whl (1.4MB)
    100% |████████████████████████████████| 1.4MB 5.9kB/s 
Installing collected packages: pip
  Found existing installation: pip 8.1.1
    Uninstalling pip-8.1.1:
      Successfully uninstalled pip-8.1.1
Successfully installed pip-9.0.3
$ sudo python get-pip.py # pip2
```

查看 pip 版本又遇问题
``` bash
$ pip --version
bash: /usr/bin/pip: 没有那个文件或目录
pip3 --version
bash: /usr/bin/pip3: 没有那个文件或目录
```

继续解决

``` bash
$ which is pip
/usr/local/bin/pip

$ type pip
pip 已经哈希操作(/usr/bin/pip)

$ hash -r
```

再次查看pip版本
```
$ pip -V
pip 9.0.3 from /usr/local/lib/python2.7/dist-packages (python 2.7)
$ pip3 -V
pip 9.0.3 from /usr/local/lib/python3.5/dist-packages (python 3.5)
```
