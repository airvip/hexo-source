---
title: 美化 Windows 终端提示符
date: 2020-09-13 11:51:00
tags: 
    - PowerShell
    - Windows
---

> 如果累了，就休息休息

大多数开发工作都是在 Windows 上进行的，当使用终端操作 Git 时，实在无法忍受 Windows 下丑陋的终端，于是决定对其进行美化处理。

<!-- more -->

我们可以使用最新的 PowerShell 来取代默认的 Windows 终端。

## 下载 PowerShell

[github 中最新稳定版 7.0.3 下载地址](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3) 

根据自己的的系统进行下载，我的是 Windows 10 X64，我下载了 PowerShell-7.0.3-win-x64.msi

下载完成后，直接全部默认安装完成即可。

我之前已经在 Microsoft Store 中安装了 Windows Terminal 感觉很好用(推荐)，可以直接在 Microsoft Store 中搜索 Terminal，正常情况下，搜索结果的第一个就是，直接获取安装即可。系统要求 Windows 10 系统 18362 或更高版本。

以管理员身份打开 Windows Terminal，新建标签页，发现多了 PowerShell 选项，选择它，新建了标签页

![windowsTerminal](https://s1.ax1x.com/2020/09/13/wwXSMR.png)

## 美化工作

安装 posh-git 模块，方便我们在提示符中对 git 的一些状态显示。

```
PS C:\Users\Admin> Install-Module posh-git                                                               
# 提示不信任的仓库，是否继续安装               
Untrusted repository
You are installing the modules from an untrusted repository. If you trust this repository, change its
InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
'PSGallery'?
# 输入 Y 进行继续安装
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
```

安装 oh-my-posh 美化的关键

```
PS C:\Users\Admin> Install-Module oh-my-posh

同上，输入 Y 进行安装
```

导入刚刚安装的这两个模块，并使其生效

```
PS C:\Users\Admin> Import-Module posh-git
C:\Users\Admin> Import-Module oh-my-posh
C:\Users\Admin> 
```

配置 Windows Terminal 终端字体

`ctrl` + `,` 键打开设置，是一个 json 格式的文件，我选择 vscode 打开，对该终端添加字体配置 `"fontFace": "Cascadia Code PL",`

```
{
    "guid": "{574e775e-4f2a-5b96-ac1e-a2962a402336}",
    "hidden": false,
    "name": "PowerShell",
    "fontFace": "Cascadia Code PL",
    "source": "Windows.Terminal.PowershellCore"
}
```

推荐使用微软新的开源字体 Cascadia Code`，可以在 系统盘:\Windows\Fonts 中搜索 Cascadia，如果没有，直接下载安装

[微软字体 Cascadia Code 下载地址](https://github.com/microsoft/cascadia-code/releases)

下载解压后，将 ttf 文件中的 4 个字体文件复制到 系统盘:\Windows\Fonts 中即可


## 修改 vscode 终端样式

将默认终端改为 pwsh

![vscodeTerminal](https://s1.ax1x.com/2020/09/13/wwXPZ6.png)

在 vscode 中打开新建终端 (我的 vscode 做了本地中文化)

* 菜单栏的 Terminal > New Terminal
* 菜单栏的 终端 > 新终端

启用配置 

`PS D:\workspace\demo> Set-Prompt`

此时颜色已经改变，但是字体有问题，打开 vscode 配置文件

* 菜单栏的 File > Preferences > Settings
* 菜单栏的 文件 > 首选项 > 设置

搜索 Font Family，将 Terminal 的字体修改为 Cascadia Code PL 即可




