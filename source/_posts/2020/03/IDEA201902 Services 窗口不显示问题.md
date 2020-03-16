---
title:IDEA201902 Services 窗口不显示问题
date: 2020-03-16 17:05:56
tags: 
    - Intellij
---

> 低头看路，抬头看天

我们在使用 Intellij idea 在开发微服务的时候，经常需要在多个服务之间切换操作，其实 idea 给我们提供了一个非常方便的功能， Services 窗口：一个管理所有服务的地方

![ideaServices](https://s1.ax1x.com/2020/03/16/8J5wSH.png)

<!-- more -->

## 开启 Services 窗口

可以查看官网 [Services Tool Window](https://www.jetbrains.com/help/idea/2019.2/services-tool-window.html)

通过 View 》 Tool Windows 》 Services 来开启 或者 使用快捷键  `Alt`+`8`

然而，经常发生的情况是上面的两种方式都不能正常开启 Services, 所以我们通过修改 workspace.xml 方式来开启

在项目的 .idea 》 workspace.xml 中找到 RunDashboard 替换成如下

```
<component name="RunDashboard">
    <option name="configurationTypes">
      <set>
        <option value="SpringBootApplicationConfigurationType" />
      </set>
    </option>
    <option name="ruleStates">
      <list>
        <RuleState>
          <option name="name" value="ConfigurationTypeDashboardGroupingRule" />
        </RuleState>
        <RuleState>
          <option name="name" value="StatusDashboardGroupingRule" />
        </RuleState>
      </list>
    </option>
</component>
```

仔细对比，其实我们只是添加了

```
<option name="configurationTypes">
    <set>
      <option value="SpringBootApplicationConfigurationType" />
    </set>
</option>

```

一般接下来就是，**关闭项目，重新打开项目**，就会发现我们心心念念的 Services 窗口已经显示出来了。

