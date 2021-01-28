---
title: curl命令发送请求详解
date: 2020-12-31 13:18:22
tags: 
	- Curl
---

> 加油吧！少年，马上进入新的一年

curl 是一个利用 URL 语法在命令行下工作的文件传输工具。它的名字就是客户端（client）的 URL 工具的意思。它的功能非常强大，命令行参数多达几十种。

<!-- more -->

## 极速体验


* 发送 GET 请求

```
root@airvip:~# curl blog.diff.wang
```

* 发送 POST 请求

```
root@airvip:~# curl blog.diff.wang -X POST -d 'login=emma&password=123'
```

## 常用参数

**-A**

`-A` 参数指定客户端的用户代理头信息，即: `User-Agent`。curl 的默认用户代理字符串是 `curl/[version]`


```
root@airvip:~# curl blog.diff.wang -X POST -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.66' -d 'login=emma&password=123'
```

上面命令将 `User-Agent` 改成 Edg 浏览器。也可以通过 `-H` 参数设置 `curl blog.diff.wang -H 'User-Agent: python/3.6' `

**-b**

`-b` 参数用来向服务器发送 Cookie

```
root@airvip:~# curl blog.diff.wang -b 'userid=123;token=123'
```

上面命令会生成一个头信息 `Cookie: userid=123;token=123` 发送给服务端

```
root@airvip:~# curl blog.diff.wang -b cookies.txt
```

上面命令读取本地文件 `cookies.txt`(可以通过 `-c` 参数生成该文件)，里面是服务器设置的 Cookie 内容，将其发送到服务器。

**-c**

`-c` 参数将服务器设置的 Cookie 写入一个文件
```
root@airvip:~# curl blog.diff.wang -c cookies.txt
```

**-d**

`-d` 参数用于发送 POST 请求的数据体

```
root@airvip:~# curl blog.diff.wang -X POST -d 'login=emma&password=123'
root@airvip:~# curl blog.diff.wang -X POST -d 'login=emma' -d 'password=123'
```

使用 `-d` 参数以后，请求会自动加上标头 `Content-Type : application/x-www-form-urlencoded`。并且会自动将请求转为 `POST` 方法，所以可以省略 `-X POST`

```
root@airvip:~# curl blog.diff.wang -X POST --data-urlencode 'login=air vip&password=123'
```
上面命令发送的数据`air vip`之间有一个空格，需要进行 URL 编码。

`--data-urlencode` 参数等同于 `-d`，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码。


```
root@airvip:~# curl blog.diff.wang -X POST -d '@/root/airvip/1.txt'
```
上面命令读取本地 `1.txt` 文件的内容，作为数据体向服务器发送。

**-e**

`-e` 参数用来设置 HTTP 的消息头 `Referer`，表示请求的来源。

```
root@airvip:~# curl blog.diff.wang -e 'http://blog.diff.wang?page=1'
```

上面命令将 `Referer` 的信息设为 `http://blog.diff.wang?page=1`,也可以通过 `-H` 参数设置 `curl blog.diff.wang -H 'http://blog.diff.wang?page=1' `

**-F**

`-F` 参数用来向服务器上传二进制文件。

```
root@airvip:~# curl blog.diff.wang -F 'avatar=@/root/airvip/1.png'
```
上面命令会给 HTTP 请求头加上 `Content-Type: multipart/form-data`，然后将文件 `1.png` 作为 `avatar` 字段上传。

`-F` 参数可以指定 MIME 类型。
```
root@airvip:~# curl blog.diff.wang -F 'avatar=@/root/airvip/1.png;type=image/png'
```
上面命令指定 MIME 类型为`image/png`，否则 curl 会把 MIME 类型设为`application/octet-stream`。

```
root@airvip:~# curl blog.diff.wang -F 'avatar=@/root/airvip/1.png;filename=airvip.png'
```
上面命令中，原始文件名为`1.png`，但是服务器接收到的文件名为`airvip.png`。

**-G**

`-G` 参数用来构造 URL 的查询字符串。
```
root@airvip:~# curl blog.diff.wang -G -d 'page=2' -d 'type=ubuntu' 
```
上面命令会发出一个 GET 请求，实际请求的 URL 为 `blog.diff.wang?page=2&type=ubuntu`。如果省略 `-G`，会发出一个 POST 请求。

如果数据需要 URL 编码，可以结合 `--data-urlencode` 参数。
```
root@airvip:~# curl blog.diff.wang -G --data-urlencode 'type=ubuntu linux'
```

**-H**

`-H` 参数添加 HTTP 请求的标头。
```
root@airvip:~# curl blog.diff.wang -H 'Accept-Language: en-US' -H 'User-Agent: python/3.6' -H 'Content-Type: application/json' -d '{"name"="airvip", "password":"123"}'
```

**-i**

`-i`参数打印出服务器回应的 HTTP 标头。

```
root@airvip:~# curl blog.diff.wang -i
```
上面命令收到服务器回应后，先输出服务器回应的标头，然后空一行，再输出网页的源码。

**-I**

`-I` 参数向服务器发出 HEAD 请求，然会将服务器返回的 HTTP 标头打印出来。
```
root@airvip:~# curl blog.diff.wang -I
root@airvip:~# curl blog.diff.wang --head
```
上面命令输出服务器对 HEAD 请求的回应, `--head` 参数等同于 `-I`。


**-k**

`-k` 参数指定跳过 SSL 检测。
```
root@airvip:~# curl https://blog.diff.wang -k
```
上面命令不会检查服务器的 SSL 证书是否正确。


**-L**

`-L` 参数会让 HTTP 请求跟随服务器的重定向。curl 默认不跟随重定向。
```
root@airvip:~# curl blog.diff.wang -L -d 'name=airvip'
```

**--limit-rate**

`--limit-rate`用来限制 HTTP 请求和回应的带宽，模拟慢网速的环境。
```
root@airvip:~# curl blog.diff.wang --limit-rate 200k
```
上面命令将带宽限制在每秒 200K 字节。

**-o**

`-o` 参数将服务器的回应保存成文件，等同于 `wget` 命令。

```
root@airvip:~# curl blog.diff.wang -o index.html
```
上面命令将 `blog.diff.wang` 保存成 `index.html`。

**-O**

`-O` 参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。
```
root@airvip:~# curl blog.diff.wang/index.html -O
```
上面命令将服务器回应保存成文件，文件名为 `index.html`。



**--trace**


`--trace` 参数也可以用于调试，还会输出原始的二进制数据。
```
root@airvip:~# curl blog.diff.wang --trace -
root@airvip:~# curl blog.diff.wang --trace trace.txt
```

**-S**

`-s` 参数将不输出错误和进度信息。
```
root@airvip:~# curl blog.diff.wang -s
```
上面命令一旦发生错误，不会显示错误信息。不发生错误的话，会正常显示运行结果。

如果想让 curl 不产生任何输出，可以使用下面的命令。
```
root@airvip:~# curl blog.diff.wang -s -o /dev/null
```

**-u**

`-u` 参数用来设置服务器认证的用户名和密码。
```
root@airvip:~# curl blog.diff.wang/login -u 'airvip:123456'
```
上面命令设置用户名为 `airvip`，密码为 `123456`，然后将其转为 HTTP 标头 `Authorization: Basic YWlydmlwOjEyMzQ1Ng==`。

curl 能够识别 URL 里面的用户名和密码。
```
root@airvip:~# curl http://airvip:123456@blog.diff.wang/login
```
上面命令能够识别 URL 里面的用户名和密码，然后将其转为 HTTP 标头 `Authorization: Basic YWlydmlwOjEyMzQ1Ng==`。

```
root@airvip:~# curl http://blog.diff.wang/login -u 'airvip'
```
上面命令只设置了用户名，执行后，curl 会提示用户输入密码。


**-v**

`-v` 参数输出通信的整个过程，用于调试。
```
root@airvip:~# curl blog.diff.wang -v
```

**-x**

`-x` 参数指定 HTTP 请求的代理。

```
root@airvip:~# curl blog.diff.wang -x socks5://airvip:123456@myproxy.com:8888 
```
上面命令指定 HTTP 请求通过 `myproxy.com:8888` 的 `socks5` 代理发出。如果没有指定代理协议，默认为 HTTP。
```
root@airvip:~# curl blog.diff.wang -x airvip:123456@myproxy.com:8888 
```
上面命令中，请求的代理使用 HTTP 协议。

**-X**

`-X` 参数指定 HTTP 请求的方法。
```
root@airvip:~# curl blog.diff.wang -X POST
```

### 参考资料

[Curl Cookbook](https://catonmat.net/cookbooks/curl)