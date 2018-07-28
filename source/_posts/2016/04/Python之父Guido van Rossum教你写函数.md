---
title: Sweetalert优雅的做出弹出提示框
date: 2016-04-25 19:20:51
tags:
    - Python
---

> 那有什么天才，只不过他比你付出的多而已。

本文为大家分享Python之父Guido van Rossum推荐的函数写法，可以大大提高这个函数的灵活性。

<!-- more -->

每个程序员在学习编程的过程中，肯定没少写过main()函数，Python程序员也不例外。

![Guido](/img/201604/python/guido.jpg)

# main() 对比

一般来说，Python程序员可能是这样写main()函数的：

```
import sys
import getopt

def main():
    try:
        opt, args = getopt.getopt(sys.argv[1:],"h",["help"])
    except getopt.error, msg:
        print msg
        print "for help use --help"
        sys.exit(2)
    for o, a in opts:
        if o in ("-h", "--help"):
            print __doc__
            sys.exit(0)
    for arg in args:
        process(arg)

if __name__ == "__main__":
    main()
```

Guido也承认之前自己写的main()函数也是类似的结构，但是这样写的灵活性还不够高，尤其是需要解析复杂的命令行选项时。
为此，他向大家提出了几点建议:
添加可选的 argv 参数
首先，修改main()函数，使其接受一个可选参数 argv，支持在交互式shell中调用该函数：
这样做，我们就可以动态地提供 argv 的值，这比下面这样写更加的灵活：
定义一个Usage()异常
另一个改进之处，就是定义一个Usage()异常，可以在main()函数最后的except子句捕捉该异常：

```
import sys
import getopt

class Usage(Exception):
    def __init__(self, msg):
    self.msg = msg
	
def main(argv = None):
    if argv is None:
        arg = sys.argv
    try:
        try:
            opts, args = getopt.getopt(argv[1:], "h", ["help"])
        except getopt.error, msg:
            raise Usage(msg)
    except Usage, err:
        print >>sys.stderr, err.msg
        print >>sys.stderr, "for help use --help"
        return 2
			
if __name__ == "__main__"
    sys.exit(main())
```

这样main()函数就只有一个退出点（exit point）了，这比之前两个退出点的做法要好。而且，参数解析重构起来也更容易：在辅助函数中引发Usage的问题不大，但是使用return 2却要求仔细处理返回值传递的问题。



