---
title: Centos中Nginx基于同一个ip配置虚拟主机
date: 2016-06-19 00:12:34
tags:
    - Nginx
    - Centos
---

> 最爱的不是下雨天，而是和你一起躲过的屋檐。

解决自己有多个域名及子域名（多个站点），只有一个公网IP的问题。大家可以通过将子域名或多个域名直接解析到同一个IP上就好。该博客将叙述如何在nginx搭建虚拟主机，来挂载多个站点。

<!-- more -->

# 本地测试

1、修改hosts文件,文件位置 `/etc/hosts`

``` bash
127.0.0.1 localhost
127.0.0.1 myshop.com
127.0.0.1 www.myshop.com
127.0.0.1 myblog.com
127.0.0.1 www.myblog.com
127.0.0.1 mydb.com
127.0.0.1 www.mydb.com
```

2、修改nginx的配置文件，注意目录路径

在 nginx.conf 文件中加入 `include /server/nginx/conf/vhosts/*.conf;`

下面是我的 nginx.conf 的配置文件

```
user  www www;
worker_processes  1;

error_log  /server/log/nginx/error.log crit;
pid        /server/nginx/logs/nginx.pid;

#Specifies the value for maximum file descriptors that can be opened by this process. 
worker_rlimit_nofile 65535;

events 
{
  use epoll;
  worker_connections 65535;
}


http {
	include       mime.types;
	default_type  application/octet-stream;

	#charset  gb2312;

	server_names_hash_bucket_size 128;
	client_header_buffer_size 32k;
	large_client_header_buffers 4 32k;
	client_max_body_size 8m;

	sendfile on;
	tcp_nopush     on;

	keepalive_timeout 60;

	tcp_nodelay on;

	fastcgi_connect_timeout 300;
	fastcgi_send_timeout 300;
	fastcgi_read_timeout 300;
	fastcgi_buffer_size 64k;
	fastcgi_buffers 4 64k;
	fastcgi_busy_buffers_size 128k;
	fastcgi_temp_file_write_size 128k;

	gzip on;
	gzip_min_length  1k;
	gzip_buffers     4 16k;
	gzip_http_version 1.0;
	gzip_comp_level 2;
	gzip_types       text/plain application/x-javascript text/css application/xml;
	gzip_vary on;
	#limit_zone  crawler  $binary_remote_addr  10m;
	log_format '$remote_addr - $remote_user [$time_local] "$request" '
	              '$status $body_bytes_sent "$http_referer" '
	              '"$http_user_agent" "$http_x_forwarded_for"';
	include /server/nginx/conf/vhosts/*.conf;
}
```

3、尽情在 `/server/nginx/conf/vhosts/` 目录下添加 XXX.conf 文件就好了，建议 xxx与域名保持一致，方便之后维护

下面是我一个站点的实例

```
server {
        listen       80;
        server_name  localhost;
	index index.html index.htm index.php;
	root /server/www/airblog;
	location ~ .*.php
	{
		#fastcgi_pass  unix:/tmp/php-cgi.sock;
		fastcgi_pass  127.0.0.1:9000;
		fastcgi_index index.php;
		include fastcgi.conf;

		
                set $path_info "";
                
                set $real_script_name $fastcgi_script_name;
                
                if ($fastcgi_script_name ~ "^(.+?\.php)(/.+)$") {
                        
                        set $real_script_name $1;
                        
                        set $path_info $2;
                }
                
                fastcgi_param SCRIPT_FILENAME $document_root$real_script_name;
                fastcgi_param SCRIPT_NAME $real_script_name;
                fastcgi_param PATH_INFO $path_info;

	}
	location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
	{
		expires 30d;
	}
	location ~ .*\.(js|css)?$
	{
		expires 1h;
	}
	
	#这种配置模式将支持thinkphp的多种路径模式重写我就不在放到一个无文件里了
        #include /server/nginx/conf/rewrite/airblog.conf;
	location / {
		if (!-e $request_filename){
           	rewrite ^/(.*)$ /index.php/$1;
            	#rewrite ^/airblog/(.*)$ /airblog/index.php/$1;
        	}
	}

	access_log  /server/log/nginx/access/airblog.log;
}
```