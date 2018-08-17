---
title: python结合selenium调用chromedriver的配置问题
date: 2018-04-19 21:56:09
tags: 
    - Chromedriver
    - Selenium
---

> 对不起，我今天一直在想你！

Selenium 是一个用于 Web 应用程序测试的工具。Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样。如果通过 Chrome 做测试，就需要安装 Chromedriver，我们在做配置的时候经常会遇到各种各样的奇葩问题。

<!-- more -->

Selenium 主要是用来做自动化测试，支持多种浏览器，当用于爬虫时，主要用来解决JavaScript渲染问题。

# Selenium、Chrome配置

前提是电脑上已经安装了 Chrome 浏览器。

[前往网站下载各种版本chrome浏览器](https://www.chromedownloads.net/chrome64win/)

**安装selenium**

启动cmd命令行（同时按住windows键和R键，在对话框内输入cmd，然后回车）


安装selenium，在弹出的窗口内输入

```
> pip install selenium
```

**下载chromedrive**

[前往网站下载chromedrive](https://npm.taobao.org/mirrors/chromedriver)

下载并解压后将 `chromedriver.exe` 放在 Python 的安装目录中。

双击 `chromedriver.exe` 可以查看内部详细版本

![chromedriver_dir](/img/201804/chrome/chromedirve_dir.jpg)


编写 Python 代码做测试

```
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

from selenium import webdriver

browser = webdriver.Chrome()
browser.get("https://www.baidu.com")
```

运行这段代码，会自动打开浏览器，然后访问百度。

效果如下

![baidu_pic](/img/201804/chrome/baidu_pic.jpg)


# 配置时踩的坑

如果程序执行错误，浏览器没有打开,可以把 Chrome 添加进环境变量。
1. 进入我的电脑->属性->高级系统设置->环境变量
2. 修改path在最后面添加 ;C:\Program Files (x86)\Google\Chrome\Application\

如果程序能打开浏览器，但是打不开网页，需要安装 chrome 对应的 chromedriver。

chromedriver 与 chrome 的对应关系表:

| chromedriver版本	| 支持的Chrome版本 |
| ---- | ---- |
| v2.41 | v66-68 |
| v2.40 | v66-68 |
| v2.39	| v66-68 |
| v2.38	| v65-67 |
| v2.37	| v64-66 |
| v2.36	| v63-65 |
| v2.35	| v62-64 |
| v2.34	| v61-63 |
| v2.33	| v60-62 |
| v2.32	| v59-61 |
| v2.31	| v58-60 |
| v2.30	| v58-60 |
| v2.29	| v56-58 |
| v2.28	| v55-57 |
| v2.27	| v54-56 |
| v2.26	| v53-55 |
| v2.25	| v53-55 |
| v2.24	| v52-54 |
| v2.23	| v51-53 |
| v2.22	| v49-52 |
| v2.21	| v46-50 |
| v2.20 | v43-48 |

亲测：
chrome 67.0.3396.87 完美适配 ChromeDriver 2.40.565498
chrome 68.0.3440.106 完美适配 ChromeDriver 2.41.578737




