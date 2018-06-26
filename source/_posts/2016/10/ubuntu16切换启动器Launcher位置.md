---
title: ubuntu16切换启动器Launcher位置
date: 2016-10-09 11:45:43
tags:
    - Ubuntu
---

> 我一直都在你身边 ，一直都在。

从 Ubuntu 11.04 中首次发布 Unity 以来，启动器 Launcher 就一直被固定在系统左侧。但从 Ubuntu 16.04 开始，用户可以手动设置将 Launcher 栏放在桌面左侧或是底部显示，目前还没办法将其移动到顶部或右侧。

<!-- more -->

# 快速修改

将启动器修改到底部

``` bash
$ gsettings set com.canonical.Unity.Launcher launcher-position Bottom
```

将启动器修改到左侧

```
$ gsettings set com.canonical.Unity.Launcher launcher-position Left
```

