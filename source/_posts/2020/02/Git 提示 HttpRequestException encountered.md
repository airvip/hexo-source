---
title: Git 提示 HttpRequestException encountered
date: 2020-02-26 13:21:56
tags: 
    - Git
---

> 只有用心灵才能看得清事物本质，真正重要的东西是肉眼无法看见的

最近在 windows 下 git push 就会提示 fatal: HttpRequestException encountered, 无论是 push 还是 pull ，都会出这个问题。

<!-- more -->

错误样式

```
D:\workspace\hexo-source>git push origin master
fatal: HttpRequestException encountered.
   发送请求时出错。
Username for 'https://github.com':
```

虽然按照提示输入账号，密码后可以正常执行，但是感觉非常不爽。

这是因为 Github 禁用了 TLS v1.0 and v1.1，必须更新 Windows 的 git 凭证管理器。

[git 凭证管理器 下载](https://github.com/Microsoft/Git-Credential-Manager-for-Windows/releases/tag/v1.14.0)


![git_warning_win10](https://s1.ax1x.com/2020/04/10/Go9ETI.png)

下载完成后安装，在执行 `git pull` 或者 `git push` 就没有 HttpRequestException encountered 的提示信息了