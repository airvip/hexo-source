---
title: vscode 自动生成头部注释和函数注释 python 版
date: 2020-11-20 18:02:51
tags: 
    - Vscode
    - Python
---

> 请记住懒人的生产力永远是无限大的

vscode 是非常爽歪歪的一款编程利器，我们只需要对用户代码片段进行定义下，就可以帮我们节省很多写不必要的代码的时间。例如：文件的头部注释，函数中的注释说明

<!-- more -->

## 急速配置

进入 File > Preferences > User Snippets 选择语言，这里以 python 为例

打开 python.json 文件添加头注释和函数注释

```
{
	// Place your snippets for python here. Each snippet is defined under a snippet name and has a prefix, body and 
	// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
	// same ids are connected.
	// Example:
	// "Print to console": {
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// }
	"HEADER": {
		"prefix": "header",
		"body": [
			"#!/usr/bin/env python3",
			"# -*- encoding: utf-8 -*-",
			"'''",
			"@文件 :$TM_FILENAME",
			"@说明 :",
			"@时间 :$CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
			"@作者 :airvip",
			"'''",
			"",
			"$0"
		]
	},
	"function": {
		"prefix": "function",
		"body": [
			"\"\"\"",
			"@description  :",            
			"---------",            
			"@param  :",            
			"-------",            
			"@Returns  :",            
			"-------", 
			"\"\"\"",
			"",
			"$0"
		]
	}
}
```

## 使用体验

新建文件输入 header

![python_header](https://s3.ax1x.com/2020/11/20/DQfd4x.png)

在函数中间输入 function
![python_function](https://s3.ax1x.com/2020/11/20/DQfaU1.png)
