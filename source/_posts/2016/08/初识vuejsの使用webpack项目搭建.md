---
title: 初识vuejsの使用webpack项目搭建
date: 2016-08-21 01:39:39
tags:
    - Vuejs
---

> 奇迹，只不过是努力的另一个名字罢了。

作为一名不合格的后端 coder,由于工作原因或是个人爱好，对于新事物的的热情丝毫没有衰减的迹象，今天就初步了解一下 vue,最重要的一点，vue 的作者是国人哦！当然 AngularJS&React 也是火的一塌糊涂。

<!-- more -->

学习前端框架最重要的一点就是要使用包管理工具，如果不使用包管理工具，那么多的依赖，也是非常头痛的一件事情。

# 关于 NPM

npm 是 node 的包管理工具，还有 yo、bower 等等。今天学习的 vue,就可以使用 npm 非常方便的来实现快速开发。当然 npm 的镜像服务器在国外，因为特殊国情的原因，在使用 npm 包管理工具的时候，可能很慢或直接拉取不到依赖，大家可以使用淘宝 cnpm 来替代 npm,只是为了加快下载速度。

这里将讲解使用 vue 的脚手架命令行工具安装项目。默认大家已经安装npm包管理工具。

# 关于 VUE
Vue.js 提供一个官方命令行工具，可用于快速搭建大型单页应用。该工具提供开箱即用的构建工具配置，带来现代化的前端开发流程。只需一分钟即可启动带热重载、保存时静态检查以及可用于生产环境的构建配置的项目   ---摘自官网

# 安装

1 全局安装vue-cli
``` bash
C:\Users\wzb>npm install -g vue-cli
```
安装成功你会看到安装的目录，我的在`C:\Users\wzb\AppData\Roaming\npm\node_modules`下面

2 创建一个基于 "webpack" 模板的新项目 demo_vue是你的项目名
``` bash
D:\download\workspaces\vue>vue init webpack demo_vue
```
![vue初始化](https://s1.ax1x.com/2020/05/22/YL7qLF.png)

3 进入项目目录，安装依赖
``` bash
D:\download\workspaces\vue>cd demo_vue
//当然是使用cnpm安装依赖
D:\download\workspaces\vue\demo_vue>cnpm install
```

4 让项目跑起来
``` bash
D:\download\workspaces\vue\demo_vue>npm run dev
> demo_vue@1.0.0 dev D:\download\workspaces\vue\demo_vue
> node build/dev-server.js
//监听
Listening at http://localhost:8080
webpack: wait until bundle finished: /index.html
webpack: wait until bundle finished: /index.html
webpack built 81a5a5cd7cc353801f35 in 211202ms
Hash: 81a5a5cd7cc353801f35
Version: webpack 1.13.2
Time: 211202ms
     Asset       Size  Chunks             Chunk Names
    app.js    1.07 MB       0  [emitted]  app
index.html  241 bytes          [emitted]
Child html-webpack-plugin for "index.html":
         Asset     Size  Chunks       Chunk Names
    index.html  21.5 kB       0
webpack: bundle is now VALID.
```

vue 会调用 node 开启服务器程序，在浏览器输入`http://localhost:8080`查看效果
![vue项目运行](https://s1.ax1x.com/2020/05/22/YL7bsU.png)
