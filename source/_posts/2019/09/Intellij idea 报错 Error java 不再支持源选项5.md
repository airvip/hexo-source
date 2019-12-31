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

![java-error](https://note.youdao.com/yws/res/11156/WEBRESOURCE34e642cc8d3fcdf9b5c6e8a4153ee5b7)

本地运行用的是 JDK11，这种错误提示应该是项目编译配置使用的 java 版本不对，需要检查下项目及 idea 环境使用的 java 编译版本配置。

# 本项目排除

* 在 Intellij 菜单栏中点击 `File` -> `Project Structure`, 检查下 "Project" 和 "Modules" 栏目中 java 版本是否与本地一致,如果不一致，改成本地使用的Java版本。

![this.Project](https://note.youdao.com/yws/res/11158/WEBRESOURCE70f97719df91ea488bc5a0ec3d758b70)
![this.Modules](https://note.youdao.com/yws/res/11160/WEBRESOURCEbd226399ab6b9576b54b90773a4b5659)

* 在 Intellij 菜单栏中点击 `Settings` -> `Bulid, Execution,Deployment` -> `Java Compiler`，Target bytecode version 设为本地 Java 版本。
![Java Compiler](https://note.youdao.com/yws/res/11162/WEBRESOURCEf4b7e8ec42cb3ffb313f267d5620efb9)

# 全局修改
在 Intellij 菜单栏中点击 `File` -> `Other Settings` -> `Settings for new projects`

把Project bytecode version 一劳永逸地配置成本地Java版本

![settings](https://note.youdao.com/yws/res/11164/WEBRESOURCEb259928314dfd738b30c2ff9be67df98)




