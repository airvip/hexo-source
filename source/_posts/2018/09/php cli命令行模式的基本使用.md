---
title: php cli命令行模式的基本使用
date: 2018-09-03 16:08:23
tags: 
    - Php
---

> 失去的不再回来，原来我已经一无所有了，那我还有什么好顾虑的！

所有的 PHP 发行版，不论是编译自源代码的版本还是预创建的版本，都在默认情况下带有一个 PHP 可执行文件。这个可执行文件可以被用来运行命令行的 PHP 程序。

<!-- more -->

php 可执行文件位置

* Windows : PHP主安装目录下，文件名是 php.exe 或者（在老版本的PHP里）是 php-cli.exe。
* Linux ： 保存在PHP安装目录的 bin/ 或者 usr/bin/ 或者 usr/local/bin/ 目录下 ，可以通过 `which is php` 查看。

**注意** CLI 模式和 CGI 模式运行时用的 PHP.INI 不是同一套配置，需要单独配置。

测试 php cli 能否正常运行，**强烈推荐将 PHP 添加到 PATH 环境变量当中**

```
$ php -v
PHP 7.0.30-1+ubuntu16.04.1+deb.sury.org+1 (cli) (built: May  2 2018 12:43:14) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
    with Zend OPcache v7.0.30-1+ubuntu16.04.1+deb.sury.org+1, Copyright (c) 1999-2017, by Zend Technologies
```

这样就可以看到 PHP 的版本信息了。

# 使用 CLI 命令

编写一个简单的 PHP 文件，看能否在命令行下运行。

```
<?php
    # filename : hello.php
    echo "Hello from php CLI";
?>
```

现在尝试在命令行下运行,通过 PHP 可执行文件调用 `hello.php` 脚本

```
$ php ~/workspace/hello.php
Hello from php CLI
```

# 使用标准输入输出

PHP CLI 会定义三个常量，以便让在命令行提示符下与解释器进行交互操作更加容易。

| 常量 | 说明 |
| ---- | ---- |
| STDIN | 标准的输入设备 |
| STDOUT | 标准的输出设备 |
| STDERR | 标准的错误设备 |

可以在自己的PHP脚本里使用这三个常量，以接受用户的输入，或者显示处理和计算的结果。

编写一个简单的 PHP 文件，做下测试。

```
<?php
    # filename : hello_name.php
    # 标准输出
    fwrite(STDOUT, "Enter your name: ");
	# 获取输入
	$name = trim(fgets(STDIN));
	# 再次输入
	fwrite(STDOUT, "Hello, $name");
?>
```

在命令行下运行下

```
$ php ~/workspace/hello_name.php
Enter your name: airvip
Hello, airvip
```

fwrite() 函数首先会向标准的输出设备写一条消息，询问用户的姓名。然后它会把从标准输入设备获得的用户输入信息读取到一个 PHP 变量里，并它把合并成为一个字符串。然后就用 fwrite() 把这个字符串打印输出到标准的输出设备上。

# 命令行自定义变量【$argv|$argc】

在命令行里输入程序参数来更改其运行方式非常常见，对 CLI 程序这样做。
PHP CLI 带有两个特殊的变量，专门用来达到这个目的：
* $argv ：通过命令行把传递给 PHP 脚本的参数保存为单独的数组元素。
* $argc ：用来保存 $argv 数组里元素的个数。

编写一个简单的 PHP 文件，做下测试。

```
<?php
    # filename : use_argv.php
    print_r($argv);
	print_r($argc);
?>
```

在命令行下运行下

```
$ php ~/workspace/use_argv.php airvip china
Array
(
    [0] => use_argv.php
    [1] => airvip
    [2] => china
)
3
```

**$argv 的第一个自变量总是脚本自己的名称**

# 命令行自定义变量【getopt】

注意：需要把 register_argc_argv 打开

getopt 使用说明
```
array getopt ( string $options [, array $longopts [, int &$optind ]] )
```

参数：

* options：该字符串中的每个字符会被当做选项字符，匹配传入脚本的选项以单个连字符(-)开头。 比如，一个选项字符串 "x" 识别了一个选项 -x。 只允许 a-z、A-Z 和 0-9。
* longopts：选项数组。此数组中的每个元素会被作为选项字符串，匹配了以两个连字符(--)传入到脚本的选项。 例如，长选项元素 "opt" 识别了一个选项 --opt。
* optind：If the optind parameter is present, then the index where argument parsing stopped will be written to this variable.

options 参数可能包含了以下元素：
* 单独的字符（不接受值）
* 后面跟随冒号的字符（此选项需要值）
* 后面跟随两个冒号的字符（此选项的值可选）

说明：options 和 longopts 的格式几乎是一样的，唯一的不同之处是 longopts 需要是选项的数组（每个元素为一个选项），而 options 需要一个字符串（每个字符是个选项）。

编写一个简单的 PHP 文件，做下测试。

```
<?php
    # filename: use_getopt.php
    $shortopts  = "";
	$shortopts .= "f:";  // Required value
	$shortopts .= "v::"; // Optional value
	$shortopts .= "abc"; // These options do not accept values

	$longopts  = [
		"required:",     // Required value
		"optional::",    // Optional value
		"option",        // No value
		"opt",           // No value
	];
	$options = getopt($shortopts, $longopts);
	var_dump($options);
?>
```

在命令行下运行下

```
$ php ~/workspace/use_getopt.php -f "value for f" -v -a --required value --optional="optional value" --option
array(6) {
  ["f"]=>
  string(11) "value for f"
  ["v"]=>
  bool(false)
  ["a"]=>
  bool(false)
  ["required"]=>
  string(5) "value"
  ["optional"]=>
  string(14) "optional value"
  ["option"]=>
  bool(false)
}
```

# 命令行变量【使用CLI参数】

除了用命令行传递PHP脚本参数，还可以传递PHP CLI参数以更改其工作方式。

| 参数	| 说明 |
| ---- | ---- |
| -a	| 交互式运行Run interactively |
| -c	| path 从path读取php的.ini文件 |
| -n	| 不用读取php的.ini文件就直接运行 |
| -m	| 列出经过编译的模块 |
| -i	| 显示有关PHP构建的信息 |
| -l	| 检查PHP脚本的句法 |
| -s	| 以彩色方式显示源代码 |
| -w	| 显示去掉注释之后的源代码 |
| -h	| 显示帮助 |

交互模式：以交互方式使用 PHP CLI，也就是输入命令，马上获得结果。

交互模式如下图所示

![php -a](/img/201809/php_cli/php_a.jpg)

同样可以不使用 -a 参数就调用 CLI 可执行文件，而是直接输入完整的脚本或者代码段，然后通过 `Ctrl+D` 按键结束来执行代码。

```
airvip@airvip:~$ php
<?php
echo date('Y-m-d H:i:s');
?>
按 Ctrl+D 组合键
2018-09-03 17:03:58
```

# 参考文章

[php cli命令 自定义参数传递](http://www.cnblogs.com/zcy_soft/archive/2011/12/10/2283437.html)


