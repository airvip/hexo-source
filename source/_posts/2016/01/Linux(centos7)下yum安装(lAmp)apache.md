---
title: Linux(centos7)下yum安装(lAmp)apache
date: 2016-01-28 18:26:23
tags:
    - Linux
    - Centos
    - Apache
---

Apache是世界使用排名第一的Web服务器软件。它可以运行在几乎所有广泛使用的计算机平台上，由于其跨平台和安全性被广泛使用，是最流行的Web服务器端软件之一。

<!-- more -->

现在通过yum来安装咱们的服务器Apache，对于初学者建议使用LAMP一键安装包，熟悉了基本操作之后，再用包管理工具安装，再进一步源码编译安装。

学习嘛！就要多折腾，这样就学起来轻松了。

## 操作平台

查看操作环境
```
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 7.1.1503 (Core) 
```

## 安装

安装apache软件

Apache软件的软件包名称叫做httpd.
```
查看是否安装Apache
[root@localhost ~]# yum list installed | grep httpd
查看yum库有没有Apache
[root@localhost ~]# yum list | grep httpd 
httpd.x86_64                            2.4.6-40.el7.centos            @base 
...............................
开始安装
[root@localhost ~]# yum -y install httpd
```

![httpd_01](/img/201601/apache/httpd_01.jpg)

如图所示：安装的Apache版本2.4.6，根据红帽官方文档说明，RHEL 7 (或CentOS 7)上可用的Apache版本正是2.4版的。

安装完成后，Apache是以httpd服务的形式存在的。因此，要启动Apache并将其设置为开机启动，就使用命令：
```
[root@localhost ~]# systemctl start httpd.service
[root@localhost ~]# systemctl enable httpd.service
ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
# 查看httpd的服务状态
[root@localhost ~]# systemctl status httpd.service
```
![httpd_02](/img/201601/apache/httpd_02.jpg)

如图所示：<span style="color:red">“enabled”表示httpd服务已设为开机启动，“active（running）”则表示httpd服务正在运行中。</span>

HTTP协议已被启动起来了，由于HTTP协议使用到tcp端口80，因此防火墙要放通tcp端口80：
```
[root@localhost ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent
```
出现success，重启防火墙让更改生效
```
[root@localhost ~]# firewall-cmd --reload
```
出现seccess,检查是否配置成功
```
[root@localhost ~]# firewall-cmd --list-all
```
![httpd_03](/img/201601/apache/httpd_03.jpg)
如图所示：tcp端口80已经开放

在物理机浏览器访问刚搭建的web服务器，因为我是在虚拟机做的，又是nat网络模式，要访问就必须做端口映射，这就是之前做了准备

不过，因为这个时候还未创建任何页面，所以它显示的是Apache软件自带的测试页面：

![httpd_04](/img/201601/apache/httpd_04.jpg)

说明：

/etc/httpd/conf.d目录下以.conf结尾的配置文件也会被读取；

可加载模块（如PHP）的配置文件应放置在/etc/httpd/conf.modules.d目录下，并且也以.conf结尾。

## httpd.conf
httpd.conf配置文件的内容（少量配置有修改，应该可以直接替换你的http.conf）：
```
#我将删除一些介绍实在太多了
# Configuration and logfile names: If the filenames you specify for many
# of the server's control files begin with "/" (or "drive:/" for Win32), the
# with ServerRoot set to '/www' will be interpreted by the
# server as '/www/log/access_log', where as '/log/access_log' will be
# interpreted as '/log/access_log'.

# ServerRoot: The top of the directory tree under which the server's
# configuration, error, and log files are kept.
#
#服务器根目录，该配置文件中没有使用绝对路径的地方，都认为是在该目录下
ServerRoot "/etc/httpd"

#
# Listen: Allows you to bind Apache to specific IP addresses and/or
# ports, instead of the default. See also the <VirtualHost>
# directive.
#
# Change this to Listen on specific IP addresses as shown below to
# prevent Apache from glomming onto all bound IP addresses.
#监听的端口号
#Listen 12.34.56.78:80
Listen 80

#
# Dynamic Shared Object (DSO) Support
#
# To be able to use the functionality of a module which was built as a DSO you
# have to place corresponding `LoadModule' lines at this location so the
# directives contained in it are actually available _before_ they are used.
# Statically compiled modules (those listed by `httpd -l') do not need
# to be loaded here.
#
# Example:
# LoadModule foo_module modules/mod_foo.so
#该设定让/etc/httpd/conf.modules.d目录下以.conf结尾的配置文件也会被读取进来。DSO对象的配置文件应放置在此目录下
Include conf.modules.d/*.conf

#
# If you wish httpd to run as a different user or group, you must run
# httpd as root initially and it will switch.
#
# User/Group: The name (or #number) of the user/group to run httpd as.
# It is usually good practice to create a dedicated user and group for
# running httpd, as with most system services.
#当你使用浏览器访问web服务器时，会产生相应的httpd进程，该进程所属的用户和组就由这里设定。
User apache
Group apache

# 'Main' server configuration
#

#
# ServerAdmin: Your address, where problems with the server should be
# e-mailed.  This address appears on some server-generated pages, such
# as error documents.  e.g. admin@your-domain.com
#服务其管理员，有问题就像这个邮箱里面发
ServerAdmin sdqhwzb@163.com

#
# ServerName gives the name and port that the server uses to identify itself.
# This can often be determined automatically, but we recommend you specify
# it explicitly to prevent problems during startup.
#
# If your host doesn't have a registered DNS name, enter its IP address here.
#设置主机名。生产环境中，该主机名应该是能被正确解析成这台服务器的IP的。
#ServerName www.example.com:80

#
# Deny access to the entirety of your server's filesystem. You must
# explicitly permit access to web content directories in other
# <Directory> blocks below.
#<Directory 目录路径>和</Directory>：用于设定指定目录（这里是系统根目录）的访问权限。 
#目录路径那里可以使用绝对路径和相对路径，还可以搭配使用通配符和正则表达式。
#AllowOverride：如果指定目录下（这里是系统根目录）存在一个.htaccess文件，
#那么AllowOverride设定的是，该文件里面的设置是否能够覆盖<Directory>这部分的设置，     
#或该文件里面的设置可以覆盖的是<Directory>这部分的哪些设置。none表示不允许进行覆盖。    
#Require：设定哪些用户可以访问该目录（这里是系统根目录）。all denied表示不允许访问该目录。
<Directory />
    AllowOverride none
    Require all denied
</Directory>

#
# DocumentRoot: The directory out of which you will serve your
# documents. By default, all requests are taken from this directory, but
# symbolic links and aliases may be used to point to other locations.
#设定网页存放目录。比如说，
#访问http://127.0.0.1/index.html实际上就是访问这个本机的/var/www/html/index.html这个文档。 
DocumentRoot "/var/www/html"

#
# Relax access to content within /var/www.
#Require all granted：all granted表示可无条件访问该目录。 
<Directory "/var/www">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

# Further relax access to the default document root:
<Directory "/var/www/html">
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #Options：用于设定在该目录中哪些特性可用。FollowSymLinks，让该目录下的符号链接可以生效。
    #默认这里还有个Indexes选项，建议拿掉。
    #它的作用是，当浏览器访问该目录（而非该目录下的某一个档案）时，
    #如果该目录下没有默认网页(如 index.html)，那么此时就会返回该目录下的文件名列表。 
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>

#
# DirectoryIndex: sets the file that Apache will serve if a directory
# is requested.
#<IfModule 模块>和</IfModule>：如果Apache有加载这里指定的模块，那么Apache就会对它们之间的语句进行处理。  
#也可以使用<IfModule!模块>和</IfModule>，那么就表示没有加载指定的模块时才进行处理。  
#这里的模块可以是模块ID（如rewrite_module），也可以是模块文件名（如mod_rewrite.c）。
#DirectoryIndex：这就是设置默认网页的，即当浏览器访问某一个目录时，会返回该目录下的index.html文件（如果存在的话）。
#也可以接多个档名，如DirectoryIndex index.html index.htm index.cgi index.pl，这样就会返回找到的第一个文件。 
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

#
# The following lines prevent .htaccess and .htpasswd files from being
# viewed by Web clients.
#<Files 文件名>和</Files>：对指定文件名的文件应用这里指定的语句。  
#这里的语句的意思是：任意目录下，文件名符合.ht*的文件都会被禁止访问。 
<Files ".ht*">
    Require all denied
</Files>

#
# ErrorLog: The location of the error log file.
# If you do not specify an ErrorLog directive within a <VirtualHost>
# container, error messages relating to that virtual host will be
# logged here.  If you *do* define an error logfile for a <VirtualHost>
# container, that host's errors will be logged there and not here.
#错误日志所在文档为/etc/httpd/logs/error_log 
ErrorLog "logs/error_log"

#
# LogLevel: Control the number of messages logged to the error_log.
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
#设置日志记录级别 
LogLevel warn

<IfModule log_config_module>
    #
    # The following directives define some format nicknames for use with
    # a CustomLog directive (see below).
    #
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common

    <IfModule logio_module>
      # You need to enable mod_logio.c to use %I and %O
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>

    #
    # The location and format of the access logfile (Common Logfile Format).
    # If you do not define any access logfiles within a <VirtualHost>
    # container, they will be logged here.  Contrariwise, if you *do*
    # define per-<VirtualHost> access logfiles, transactions will be
    # logged therein and *not* in this file.
    #
    #CustomLog "logs/access_log" common

    #
    # If you prefer a logfile with access, agent, and referer information
    # (Combined Logfile Format) you can use the following directive.
    #客户端访问服务器的日志会记在/etc/httpd/logs/access_log  
    CustomLog "logs/access_log" combined
</IfModule>

<IfModule alias_module>
    #
    # Redirect: Allows you to tell clients about documents that used to
    # exist in your server's namespace, but do not anymore. The client
    # will make a new request for the document at its new location.
    # Example:
    # Redirect permanent /foo http://www.example.com/bar

    #
    # Alias: Maps web paths into filesystem paths and is used to
    # access content that does not live under the DocumentRoot.
    # Example:
    # Alias /webpath /full/filesystem/path
    #
    # If you include a trailing / on /webpath then the server will
    # require it to be present in the URL.  You will also likely
    # need to provide a <Directory> section to allow access to
    # the filesystem path.

    #
    # ScriptAlias: This controls which directories contain server scripts.
    # ScriptAliases are essentially the same as Aliases, except that
    # documents in the target directory are treated as applications and
    # run by the server when requested rather than as documents sent to the
    # client.  The same rules about trailing "/" apply to ScriptAlias
    # directives as to Alias.
    #ScriptAlias会将URL路径映射到指定目录，并且让该目录具有CGI脚本执行权限（因此CGI脚本都可放置在该目录下）。
    #以这里的设定举个例子，假如在浏览器中输入http://example.com/cgi-bin/foo，此时就会执行/var/www/cgi-bin/foo这个CGI脚本。 
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"

</IfModule>

#
# "/var/www/cgi-bin" should be changed to whatever your ScriptAliased
# CGI directory exists, if you have that configured.
#
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>

#这部分是关于MIME的
<IfModule mime_module>
    #
    # TypesConfig points to the file containing the list of mappings from
    # filename extension to MIME-type.
    #
    TypesConfig /etc/mime.types

    #
    # AddType allows you to add to or override the MIME configuration
    # file specified in TypesConfig for specific file types.
    #
    #AddType application/x-gzip .tgz
    #
    # AddEncoding allows you to have certain browsers uncompress
    # information on the fly. Note: Not all browsers support this.
    #
    #AddEncoding x-compress .Z
    #AddEncoding x-gzip .gz .tgz
    #
    # If the AddEncoding directives above are commented-out, then you
    # probably should define those extensions to indicate media types:
    #
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz

    #
    # AddHandler allows you to map certain file extensions to "handlers":
    # actions unrelated to filetype. These can be either built into the server
    # or added with the Action directive (see below)
    #
    # To use CGI scripts outside of ScriptAliased directories:
    # (You will also need to add "ExecCGI" to the "Options" directive.)
    #
    #AddHandler cgi-script .cgi

    # For type maps (negotiated resources):
    #AddHandler type-map var

    #
    # Filters allow you to process content before it is sent to the client.
    #
    # To parse .shtml files for server-side includes (SSI):
    # (You will also need to add "Includes" to the "Options" directive.)
    #
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>

#
# Specify a default charset for all content served; this enables
# interpretation of all content as UTF-8 by default.  To use the
# default browser choice (ISO-8859-1), or to allow the META tags
# in HTML content to override this choice, comment out this
# directive:
#AddDefaultCharset会强制客户端浏览器使用指定的字符集编码方式。这可能会有问题，所以要将它关闭。
#实际上，所使用的编码方式在网页里有声明。(AddDefaultCharset Off )  
AddDefaultCharset UTF-8

<IfModule mime_magic_module>
    #
    # The mod_mime_magic module allows the server to use various hints from the
    # contents of the file itself to determine its type.  The MIMEMagicFile
    # directive tells the module where the hint definitions are located.
    #
    MIMEMagicFile conf/magic
</IfModule>

#
# Customizable error responses come in three flavors:
# 1) plain text 2) local redirects 3) external redirects
#
# Some examples:
#ErrorDocument 500 "The server made a boo boo."
#ErrorDocument 404 /missing.html
#ErrorDocument 404 "/cgi-bin/missing_handler.pl"
#ErrorDocument 402 http://www.example.com/subscription_info.html
#

#
# EnableMMAP and EnableSendfile: On systems that support it,
# memory-mapping or the sendfile syscall may be used to deliver
# files.  This usually improves server performance, but must
# be turned off when serving from networked-mounted
# filesystems or if support for these functions is otherwise
# broken on your system.
# Defaults if commented: EnableMMAP On, EnableSendfile Off
#
#EnableMMAP off
#允许Apache使用系统核心支持的sendfile来传送文件给客户端
EnableSendfile on

# Supplemental configuration
#
# Load config files in the "/etc/httpd/conf.d" directory, if any.
#在/etc/httpd/conf.d目录下以.conf结尾的配置文件也会被读取 
IncludeOptional conf.d/*.conf
```

**说明**

网页文档可以放置在/var/www/html目录下;
CGI脚本可以放置在/var/www/cgi-bin目录下；
错误日志在/etc/httpd/logs/error_log;
访问日志在/etc/httpd/logs/access_log。
设定好配置文件后，要测试配置文件语法有没有问题，可使用命令
```
[root@localhost conf]# apachectl configtest
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using localhost.localdomain. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```
产生的信息是说没有配置主机名#ServerName www.example.com:80，语法OK（我会放在vhost里面）

重启httpd服务
```
[root@localhost conf]# systemctl restart httpd
```