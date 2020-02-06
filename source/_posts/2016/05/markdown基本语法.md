---
title: markdown基本语法
date: 2016-05-11 22:26:32
tags:
    - Markdown
---

> 时间很贪婪——有时候，它会独自吞噬所有的细节。

Markdown 是一种轻量级的标记语言，由 John Gruber 和 Aaron Swartz 创建，使其成为可读性最大并可再发行的可输入输出的格式。这种语言创建灵感来自于已经存在的带标记的电子邮件文本。

<!-- more -->

Markdown 的语法简洁明了、学习容易，而且功能比纯文本更强，因此有很多人用它写博客。世界上最流行的博客平台 WordPress 和大型 CMS 如 joomla、drupal 都能很好的支持 Markdown。

**下面图片的显示效果均是 github 仓库中 md 文件的显示效果**

# h1~h6
``` bash
#这是h1标签
#这是h2标签
##这是h3标签
##这是h4标签
###这是h5标签
###这是h6标签
```
![h1_h6](https://s2.ax1x.com/2020/02/06/1yYteg.jpg)

# p段落
``` bash
这是一个p段落，也就是一个块元素，然而下面的文字是怎么换行的呢？
哦！原来只需要两个空格键就变成`<br/>`换行了。
_倾斜_  
*倾斜*  
__加粗__  
**加粗**  
***粗斜体***  
___粗斜体___  
`代码标记`
```
![pib](https://s2.ax1x.com/2020/02/06/1yYJOS.jpg)

# 无序列表与分割线
``` bash
* 你好，我是airvip
* 我来自中国
* 我们的母亲河是黄河  

--------------------------
- 你好，我是airvip
- 我来自中国
- 我们的母亲河是黄河  

==================================
+ 你好，我是airvip
    - 你好，我是阿尔维奇
+ 我来自中国
+ 我们的母亲河是黄河
```
![list](https://s2.ax1x.com/2020/02/06/1yYUoj.jpg)

# 有序列表
``` bash
1. 第一排的都是好学生
2. 第二排的都会认真听课
3. 我往往坐在最后一排听课
```

# 引用
``` bash
>我们来引用一段文字，看看效果怎么样
>
>我们只是加你简单的做个例子
```

# 链接与图片
``` bash
[这是一个连接](http://www.dear521.com)
![这是dear521.com的icon](http://www.dear521.com/Public/img/ico_48.ico)
<img src="http://www.dear521.com/Public/img/ico_48.ico" alt="alt text" title="Title" />
```

# 上标与下标
``` bash
上标：X<sub>2</sub>，下标：O<sup>2</sup>
```

![list1](https://s2.ax1x.com/2020/02/06/1yYdFs.jpg)


# 编程语言代码
去除每个反斜线
``` bash
\`\`\` javascript
$(function(){
    console.log("Hello world!");
});
\`\`\`

\`\`\` html
<div style="color:bule">这是一个块文本</div>
\`\`\`
```

# table表格

``` bash
| 姓名  | 性别 |
| ------ | ------ |
| 阿尔维奇  | 男  |
| 多尔维雅  | 女  |
| 阿尔贝尔  | 男  |
```

![table](https://s2.ax1x.com/2020/02/06/1yYGy8.jpg)