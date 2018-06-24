---
title: The requested URL...was not found
date: 2016-09-22 13:47:23
tags:
    - Apache
---

> 既不回头，何必不忘；既然无缘，何须誓言。

Apache服务器提示The requested URL *** was not found on this server错误解决办法。

<!-- more -->

问题非常简单，是由于我们服务的重写模块没有生效导致项目无法正常运行，报了上述错误。

# rewrite_module配置

检测你的apache开启了rewrite_module模块，

如果开启了Apache的rewrite_module模块：在项目目录添加.htaccess文件。

如果没有开启了Apache的rewrite_module模块：
找到apache的httpd.conf文件，启用这个模块,就是将前面"#"去掉，如果不存在则添加下面的代码。

``` bash
LoadModule rewrite_module modules/mod_rewrite.so
```

如果你的网站是根目录的话：找到

``` bash
<Directory />
  Options FollowSymLinks
  AllowOverride None
</Directory>
```

将上面的None改为All

如果你的站点不在根目录，设置如下：

``` bash
<Directory "/var/www/html/directory_name"> 
  Order allow,deny
  Allow from all
  AllowOverride All
</Directory>
```

最后重启服务器，`service httpd restart` ,这样.htaccess就可以使用了
