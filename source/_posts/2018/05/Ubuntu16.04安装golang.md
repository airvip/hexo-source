---
title: Ubuntu16.04安装golang
date: 2018-05-02 11:20:23
tags: 
    - Ubuntu
    - Golang
---

> 未来的事无人知晓，所以它才如同这重逢一般，拥有着无限的可能性。

Go语言是谷歌2009发布的第二款开源编程语言。Go语言专门针对多处理器系统应用程序的编程进行了优化，使用Go编译的程序可以媲美C或C++代码的速度，而且更加安全、支持并行进程。安装golang我们可以使用Ubuntu的包管理器直接进行安装，同样可以手动安装最新稳定版本。 

<!-- more -->

# go的环境变量

**GOROOT** 
golang的安装目录

**GOPATH** 
不要把GOPATH设置为golang的安装目录
`go install`, `go get` 及go的工具等会用到GOPATH环境变量 
作为编译后二进制文件的存放位置和import包时的搜索路径，go先搜索标准包目录，然后搜索GOPATH，所有非标准包都放在GOPATH下。（也是我们的工作目录）

如我们把 /var/www/go 作为我们的GOPATH，在该工作目录下主要设置三个目录：bin, pkg, src
bin:主要存放生成可执行文件，可自动生成。
pkg：主要存放编译后生成的包的目标文件，主要是 *.a 中间文件，可自动生成。
src：主要存放go的源文件，这里面每一个目录，就是一个包。我们可以在该目录下创建自己的go源文件。按照golang默认约定，`go run`, `go install`等命令在此目录下执行。


# 通过apt安装

查看软件源里是否存在golang

``` bash
$ sudo apt-cache search golang
dh-golang - debhelper add-on for packaging software written in Go (golang)
golang - Go programming language compiler - metapackage
golang-1.6 - Go programming language compiler - metapackage
...
```

# 直接安装

```
$ sudo apt-get -y install golang
```

一顿操作猛如虎，原来只有两毛五，这样安装只能安装golang1.6,当然我们更喜欢使用稳定的最新版本，请看下面。

# 手动安装最新稳定版（推荐）

先下载 golang 安装包
[官方下载地址](https://golang.org/dl) 如果打不开请使用国内的golang 中国
[golang 中国下载地址](https://www.golangtc.com/download)
以最新稳定版 go1.9.2 为例

```
$ cd /usr/local
$ wget https://www.golangtc.com/static/go/1.9.2/go1.9.2.linux-amd64.tar.gz
```

下载完成，进行解压

```
$ sudo -zxvf go1.9.2.linux-amd64.tar.gz
```

验证安装结果

```
$ /usr/local/go/bin/go version
go version go1.9.2 linux/amd64
```

设置PATH环境变量及GOPATH环境变量

```
$ vim /etc/profile
# 在最后加入下面这三行
export GOROOT=/usr/local/go # golang安装目录
export PATH=$GOROOT/bin:$PATH
export GOPATH=/var/www/go  # golang项目目录
```

保存后，重新加载 profile 文件，加载环境变量到内存。

```
$ source /etc/profile
```

验证一下是否配置成功，查看安装版本
![go env](/img/201805/golang/go_env.png)

# 做个简单程序

进入工作目录

```
$ cd /var/www/go/src/
``

创建项目目录及文件(project/test/main/main.go)

```
$ mkdir project
$ cd project/
$ mkdir test
$ cd test/
$ mkdir main
$ cd main/
$ vim main.go
```

输入下面的内容

```
package main

import "fmt"

func main(){
    fmt.Println("Hello Golang!")
}
```

使用 go install 工具编译此程序

```
$ cd /var/www/go/src
$ go install project/test/main/
```

查看运行编译好的二进制文件

```
$ cd /var/www/go/bin
$ ls
main
$ ./main 
Hello Golang!
```


