---
title: Python中如何将字符串作为变量名使用
date: 2016-09-16 14:14:36
tags:
    - Python
---

> 哥们很骄傲的说，我写了个很NB能让Windows崩溃的程序。旁边一妹纸冷漠的说，在一开始装系统的不就自带了吗？

有时候在开发中我们需要把某些传递的字符串作为变量来使用，这样就可以直接读取变量中的信息。

<!-- more -->

我们在 Python 中可以通过三种方式实现这一功能。

# locals()

locals() 是 python 内置的函数，通过该函数我们可以以字典的方式去访问局部和全局变量。
python 的每个模块，每个函数都有自己的名字空间，记录着变量，常量，类的命名和值。
与 js 类似，python 在使用变量时，会按照下面的步骤去搜索

1. 函数或类的局部变量
2. 全局变量
3. 内置变量

上面的三个步骤，一步步向下查找。如果在这三个步骤都找不到，就会抛出异常。

locals() 与 globals() 的区别

* locals() 是只读的。globals() 不是。这里说的只读，是值对于原有变量的只读。其实还可以对 locals() 赋值的。
* globals 返回的是当前模块的全局变量 locals 返回的是局部变量。注意，locals返回的是当前所在最小命名空间的局部变量的一个拷贝。

代码示例

``` Python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

list1 = [1, 2, 3]
print(locals())
# 全局使用会打印: __spec__, __file__, __builtins__, __cached__, __doc__, __package__, __loader__, __name__, list1 这些变量的整个字典信息
print(locals()['list1'])
# 打印出变量信息 [1, 2, 3]

def foo(args):
    name = 'airvip'
    print(locals())
    # 局部使用只打印 {'name': 'airvip', 'args': 'name'} 的字典信息
    print(locals()[args])
    # 打印出变量信息 airvip

foo('airvip')
```

# vars()

vars() 返回对象 object 的属性和属性值的字典对象。如果默认不输入参数，就打印当前调用位置的属性和属性值，相当于locals()的功能。如果有参数输入，就只打印这个参数相应的属性和属性值。


代码示例

``` Python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

list1 = [1, 2, 3]
print(vars())
# 打印 list1, __cached__, __doc__, __file__, __name__, __builtins__, __spec__, __package__, __loader__ 这些变量的整个字典信息
print(vars()['list1'])
# 打印出变量信息 [1, 2, 3]


class Foo(object):
    name = 'airvip'


print(Foo())
# 打印 <__main__.Foo object at 0x0000019D9CCDABA8>
foo = Foo
print(vars(foo))
# 打印 name, __doc__, __weakref__, __module__, __dict__ 这些变量的整个字典信息
print(vars(foo)['name'])
# 打印出变量信息 airvip 
```

# eval()

eval() 函数十分强大，官方 demo 解释为：将字符串当成有效的表达式来求值并返回计算结果。
结合 math 可以作为一个计算器，其他用法：可以将字符串类型的 list,tuple,dict 转化为对应的类型。

代码示例

``` Python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-


name = 'airvip'

print(eval('name'))
# 直接打印变量信息 airvip


list1 = eval("[[1, 2, 3],[1, 2, 3]]")
print(list1)
# 打印结果 [[1, 2, 3],[1, 2, 3]]
print(type(list1))
# 打印类型 <class 'list'>

print(eval("1,"))
# 打印结果 (1,)
print(eval("{'name':'airvip'}"))
# 打印结果 {'name': 'airvip'}
```

强大的函数是有代价的。安全性是其最大的缺点。
例如：需要用户输入一个表达式，并求值。

如果用户恶意输入，例如：`__import__('os').system('dir')` 在执行 eval() 之后，你会发现，当前目录文件都会展现在用户前面。
继续输入： `open('文件名').read()` 代码获取完毕，一条删除命令，文件消失。

怎么避免安全问题呢？
1. 自行写检查函数；
2. 使用ast.literal_eval