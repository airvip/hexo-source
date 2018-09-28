---
title: Python3.6中更好的字符串格式化
date: 2018-05-07 11:34:53
tags: 
    - Python
---

> 秩序是恶棍们的通行证，真正的美好应如无形的虚空，你是否仍感幸福？

不管在任何语言中字符串打印都是非常重要的编程手段。在 Python3.6 之前虽然也有字符串格式化打印，但总感觉有点反人类，使用的非常不舒服，尤其是在代码维护的时候，如果需要格式化的字符串比较长，就要好好缕缕如何控制样式输出了。

<!-- more -->

Python3.6 之后，f-string 是格式化字符串的一种非常方便的新方法。与其他格式化方式相比，不仅更易读，更简洁，不易出错，而且速度更快！

# 旧石器时代

最初我们想要快速的将表达式嵌入到字符串文本中经常使用：`％-formatting`

 %-formatting 这是 Python 格式化的 OG(original generation)。现在很多的 Python 文档中不建议使用 ％ 格式，如下说明：

```
The formatting operations described here exhibit a variety of quirks that 
lead to a number of common errors (such as failing to display tuples and 
dictionaries correctly). Using the newer formatted string literals or 
the str.format() interface helps avoid these errors. These alternatives 
also provide more powerful, flexible and extensible approaches to formatting text.
```

如何使用 %-formatting

```
>>> 'my name is %s' % 'airip'
'my name is airip'

# 为了插入多个变量，您必须使用这些变量的元组。
>>> 'my name is %s, my age is %d' % ('airip', 20)
'my name is airip, my age is 20'

>>> print('my name is %s, my age is %d' % ('airip', 20))
my name is airip, my age is 20
```

一旦程序中使用几个参数和更长的字符串，代码将很快变得不太容易阅读。

# 革命时期

`str.format()` 是在 Python2.6 中引入的，一定程度上解决了旧石器时代的不便，但还是不尽人意。
`str.format()` 是对 %-formatting 的改进。使用正常的函数调用语法，并通过对要转换为字符串的对象的 `__format __()` 方法进行扩展。

如何使用 str.format()

在 `str.format()` 中替换的字段用大括号标记

```
>>> 'my name is {}'.format('airip')
'my name is airip'

>>> 'my name is {}, my age is {}'.format('airip', 20)
'my name is airip, my age is 20'

# 添加下标
>>> 'my name is {1}, my age is {0}'.format(20, 'airvip')
'my name is airvip, my age is 20'

# 使用字典
>>> 'my name is {name}, my age is {age}'.format(**{'age':20, 'name':'airvip'})
'my name is airvip, my age is 20'
>>> dic = {'age':20, 'name':'airvip'}

>>> 'my name is {name}, my age is {age}'.format(**dic)
'my name is airvip, my age is 20'
```

使用 str.format() 的代码比使用 %-formatting 的代码易读，但当处理多个参数和更长的字符串时，str.format() 仍然非常冗长。
即便使用字典赋值可以压缩代码量，但字符串中的键值使用也是麻烦的很。

# 美好的现在

f-Strings 是一种改进的字符串格式化新方式。f 字符串可以节省很多的空间，阅读也非常方便，在 Python3.6 中加入了标准库中。

f-Strings 也被称为“格式化字符串文字”，f字符串是开头有一个 f 的字符串文字，以及包含表达式的大括号将被其值替换。表达式在运行时进行渲染，然后使用 `__format__` 协议进行格式化。

如何使用 f-Strings

f-Strings 语法非常简单，字符串中的大括号的表达式将直接被替换成实际结果值，小写字母 f 与大写字母 F 是一样的效果。
```
>>> name = 'airvip'
>>> age = 20
>>> f'my name is {name}, my age is {age}'
'my name is airvip, my age is 20'

>>> f'my name is {name.upper()}, my age is {age}'
'my name is AIRVIP, my age is 20'

>>> f'my name is {name.upper()}, my age is {2 * 10}'
'my name is AIRVIP, my age is 20'

# 多行 f-string
>>> info = f"""
... my name is {name},
... my age is {age}.
... """
>>> print(info)

my name is airvip,
my age is 20.
```

f-Strings 不仅使用起来非常简单方便，而且速度也更快！
f-Strings 比 ％-formatting 和 str.format() 都快。 
f-Strings 是运行时渲染的表达式，而不是常量值，在运行时，大括号内的表达式将在其自己的作用域中进行求值，然后将其与其余字符串组合在一起。