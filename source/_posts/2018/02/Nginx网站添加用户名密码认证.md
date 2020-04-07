---
title: Nginx网站添加用户名密码认证
date: 2018-02-02 02:02:37
tags:
    - Nginx
    - Htpasswd
---

> 成功只是偶然，付出就有回报并不是公理

Nginx 作为 web server 的服务。因业务需要在访问网站时，需要加一层认证信息，以防止外人直接访问网站。需求类似于 Apache 那样为指定的目录添加访问验证，一般在 Apache 下使用 htpasswd 来添加，对于使用 Nginx 的网站同样也可以使用 htpasswd 来添加验证。

<!-- more -->

# 安装平台

查看系统信息
``` bash
$ uname -a
Linux iZ2569alptcZ 3.10.0-514.6.1.el7.x86_64 #1 SMP
 Wed Jan 18 13:06:36 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
$ cat /etc/redhat-release
CentOS Linux release 7.3.1611 (Core)
```

# 安装 httpd 服务

查看系统是否安装 httpd
``` bash
$ yum list installed | grep httpd
```

没有安装，直接使用 yum 安装服务
``` bash
$ yum -y install httpd
```

# 配置认证

使用 htpasswd 命令创建认证信息文件
``` bash
$ htpasswd -c /usr/local/nginx/passwd.db air  //创建认证信息，air 为认证用户名
New password: air  //输入认证密码 
Re-type new password: air  //再次输入认证密码 
Adding password for user air
$ chmod 400 /usr/local/nginx/passwd.db  //修改网站认证数据库权限
$ chown www:www /usr/local/nginx/passwd.db  //修改网站认证数据库属主和属组
$ cat /usr/local/nginx/passwd.db  //可以通过查看文件查看用户信息，密码为加密格式 
air:r6IADXWCzlC3g
```

为 Nginx 的某站点添加认证配置
``` bash
server {
    listen       80;
    server_name  www.admin.com admin.com;
    root   "D:/phpStudy/WWW/admin/public";
    
    location / {
        # 添加下面这两行
        auth_basic "Restricted";  #虚拟主机认证命名 
        auth_basic_user_file /usr/local/nginx/passwd.db; #虚拟主机用户名密码认证数据库
        # 默认首页
        index  index.html index.htm index.php;
        #autoindex  on;
        if (!-e $request_filename) {
            rewrite  ^/(.*)$  /index.php?s=/$1  last;
            break;
        }
    }

    location ~ \.php(.*)$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param  PATH_INFO  $fastcgi_path_info;
        fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
        include        fastcgi_params;
    }
}
```

使 Nginx 重新加载配置文件
``` bash
$ sudo /etc/init.d/nginx reload
```

搞定收工，查看效果如下
![result htpasswd](https://s1.ax1x.com/2020/04/07/Gc9EJ1.png)
