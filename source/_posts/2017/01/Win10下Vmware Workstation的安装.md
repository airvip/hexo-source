---
title: Win10下Vmware Workstation的安装
date: 2017-01-05 14:18:41
tags: 
    - 虚拟机 
    - Vmware
---

> 工欲善其事必先利其器。

有时候做开发需要经常折腾系统，在没有100%把握的情况下，直接在物理机上操作可能会造成严重的后果，并且使用虚拟机还可以让自己不断地尝试些新东西，所以弄款虚拟机，这样就可以放心大胆的搞破坏了。

<!-- more -->

## VMware Workstation 简介

VMware Workstation Pro 确立了在单台 Linux 或 Windows PC 上运行多个操作系统的行业标准。

[VMware Workstation 官方网站下载传送门](https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html)

Workstation 14 Pro 借助 VMware 虚拟硬件版本 14 的功能特性进一步完善了领先的桌面 hypervisor，上述功能特性包括：虚拟网络模拟、经过更新的最新Windows 及 Linux 操作系统支持、新的基于虚拟化的安全性功能等。

## VMware Workstation 14 Pro 安装步骤

默认已经下载好 VMware Workstation 14 Pro 软件包。

1. 双击 VMware Workstation 14 Pro 软件包安装文件，或者右键管理员身份打开，提示是否允许更改，点击是；

2. 打开VMware安装向导，点击下一步；
![VMware安装向导](/img/201701/vm/vm1.png)

3. 同意最终用户协议，点击下一步；
![同意最终用户协议](/img/201701/vm/vm2.png)

4. 默认安装在C盘，点击“更改”选择安装位置；
![安装位置](/img/201701/vm/vm3.png)

5. 用户体验设置，看个人喜好选择性勾选；
![用户体验设置](/img/201701/vm/vm4.png)

6. 选择生成快捷方式；
![选择生成快捷方式](/img/201701/vm/vm5.png)

7. 进行安装；
![进行安装](/img/201701/vm/vm6.png)

8. 如果有秘钥就点击许可证，没有直接点击完成，我有秘钥就选择许可证，如果当时没有可以之后再激活。
![激活或完成](/img/201701/vm/vm7.png)

9. 输入许可证秘钥；
![输入许可证秘钥](/img/201701/vm/vm8.png)

10. 完成安装；
![完成安装](/img/201701/vm/vm9.png)

在第八步或者第十步点击完成后可自由选择是否立即重启电脑，因为只有重启后 vmware 的一些配置才会生效。


## VMware 2017 v14.x 永久许可证激活密钥

* FF31K-AHZD1-H8ETZ-8WWEZ-WUUVA
* CV7T2-6WY5Q-48EWP-ZXY7X-QGUWD

## 常见问题

### VMware 14.0 启动虚拟机系统黑屏
经总结主要原因是14版本之后注册了两个LSP协议（vSockets DGRAM、vSockets STREAM）导致异常！
解决方法：使用LSP修复工具（例如：360安全卫士/金山毒霸里的LSP工具）修复LSP网络协议，或者重置下网络链接Winsock，即打开命令提示符cmd.exe，输入命令netsh winsock reset，重启系统即可解决！

### VMware启动后假死卡住了
解决方法：关闭系统防火墙即可！





