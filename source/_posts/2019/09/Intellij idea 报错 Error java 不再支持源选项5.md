---
title: Intellij idea 报错 Error java 不再支持源选项5
date: 2019-09-09 13:21:56
tags: 
    - Maven
    - Java
---

> 埋头种地,抬头看天。

刚换了 idea, 在 Intellij 中新建了一个 Maven 项目，编译时报了如下错误：

```
Error:java: 不再支持源选项 5。请使用 6 或更高版本。
```

<!-- more  -->

![java-error](https://s2.ax1x.com/2020/01/02/lYY4fg.png)

本地运行用的是 JDK11，这种错误提示应该是项目编译配置使用的 java 版本不对，需要检查下项目及 idea 环境使用的 java 编译版本配置。

# 本项目排除

* 在 Intellij 菜单栏中点击 `File` -> `Project Structure`, 检查下 "Project" 和 "Modules" 栏目中 java 版本是否与本地一致,如果不一致，改成本地使用的Java版本。

![this.Project](https://s2.ax1x.com/2020/01/02/lYYolj.png)
![this.Modules](https://s2.ax1x.com/2020/01/02/lYYT6s.png)

* 在 Intellij 菜单栏中点击 `Settings` -> `Bulid, Execution,Deployment` -> `Java Compiler`，Target bytecode version 设为本地 Java 版本。
![Java Compiler](https://s2.ax1x.com/2020/01/02/lYY7Xn.png)

# 全局修改
在 Intellij 菜单栏中点击 `File` -> `Other Settings` -> `Settings for new projects`

把Project bytecode version 一劳永逸地配置成本地Java版本

![settings](https://s2.ax1x.com/2020/01/02/lYYhtS.png)




