---
title: 部署thinkphp项目URL重写，nginx支持pathinfo模式
date: 2016-01-07 11:03:22
tags:
    - Nginx
    - Thinkphp
---

> 这个世界上没有完美的人类，所以，人无法自己一个人活著。

我们可以通过URL重写隐藏应用的入口文件index.php，达到伪静态效果，可以增强搜索蜘蛛的友好性。

<!-- more -->

## Apache重写

1、httpd.conf配置文件中加载了mod_rewrite.so模块

2、AllowOverride None 将None改为 All
```
<Directory "/server/www">
    Options Indexes FollowSymLinks
    AllowOverride all
    Require all granted
</Directory>
```
3、把下面的内容保存为.htaccess文件放到应用入口文件的同级目录下
```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
</IfModule>
```

## IIS重写

如果你的服务器环境支持ISAPI_Rewrite的话，可以配置httpd.ini文件，添加下面的内容：
```
RewriteRule (.*)$ /index\.php\?s=$1 [I]
```
在IIS的高版本下面可以配置web.Config，在中间添加rewrite节点：
```
<rewrite>
    <rules>
        <rule name="OrgPage" stopProcessing="true">
            <match url="^(.*)$" />
            <conditions logicalGrouping="MatchAll">
                <add input="{HTTP_HOST}" pattern="^(.*)$" />
                <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
            </conditions>
            <action type="Rewrite" url="index.php/{R:1}" />
        </rule>
    </rules>
</rewrite>
```

## Nginx重写

在Nginx低版本中，是不支持PATHINFO的，但是可以通过在Nginx.conf中配置转发规则实现：

```
location / { // …..省略部分代码
    if (!-e $request_filename) {
        rewrite  ^(.*)$  /index.php?s=$1  last;
        break;
    }
}
```

<span style="color:green">其实内部是转发到了ThinkPHP提供的兼容模式的URL，利用这种方式，可以解决其他不支持PATHINFO的WEB服务器环境。</span>

如果你的ThinkPHP安装在二级目录，Nginx的伪静态方法设置如下，其中youdomain是所在的目录名称。
```
location /youdomain/ {
    if (!-e $request_filename){
        rewrite  ^/youdomain/(.*)$  /youdomain/index.php?s=$1  last;
    }
}
```
原来的访问URL：

`http://serverName/index.php/模块/控制器/操作/[参数名/参数值...]`

设置后，我们可以采用下面的方式访问：

`http://serverName/模块/控制器/操作/[参数名/参数值...]`