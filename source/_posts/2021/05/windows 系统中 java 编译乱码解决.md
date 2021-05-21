---
title: windows 系统中 java 编译乱码解决
date: 2021-05-21 09:24:11
tags: 
	- Java
	- Windows
---

> 我觉得，不改变也很好。

无论学习什么语言，我们最终还是想要使用运行。那 Java 程序是如何运行的呢？编写的 `Xxx.java` 文件，即 Java 源码文件，本质上是一个文本文件，就需要用 javac 把 `Xxx.java` 编译成字节码文件 `Xxx.class`，再用 java 命令执行这个字节码文件。

<!-- more -->

 `Xxx.java`(源码)  --compile--> `Xxx.class`(字节码)  --execute--> `在 JVM 上运行`
 
`可执行文件 javac` 就是我们所说的编译器； `可执行文件 java` 就是我们所说的虚拟机。

## java 源码

```
import java.util.*;

/*
根据name查找 score，并利用 Map 充当缓存，以提高查找效率：
*/

public class Main {
    public static void main(String[] args) {
        List<Student> list =  new ArrayList<>();
        list.add(new Student("Girl", 99));
        list.add(new Student("Boy", 98));
        list.add(new Student("Air", 100));
        list.add(new Student("Vip", 97));
        Students holder = new Students(list);
        System.out.println(holder.getScore("Boy") == 98 ? "测试成功!" : "测试失败!");
        System.out.println(holder.getScore("Air") == 100 ? "测试成功!" : "测试失败!");
        System.out.println(holder.getScore("Lisa") == -1 ? "测试成功!" : "测试失败!");
    }
}

class Student {
    String name;
    int score;

    Student(String name, int score) {
        this.name = name;
        this.score = score;
    }
}

class Students {
    List<Student> list;
    Map<String, Integer> cache;

    Students(List<Student> list) {
        this.list = list;
        cache = new HashMap<>();
    }

    /**
     * 根据name查找score，找到返回score，未找到返回-1
     */
    int getScore(String name) {
        // 先在 Map 中查找:
        Integer score = this.cache.get(name);
		// 查询不到，为 null
        if (score == null) {
			// 在 List 中查找
            score = findInList(name);
			// 查找到，不为 null，存入 Map
            if (score != null) {
                this.cache.put(name, score );
            }
        }
        return score == null ? -1 : score.intValue();
    }

    Integer findInList(String name) {
        for (Student ss : this.list) {
            if (ss.name.equals(name)) {
                return ss.score;
            }
        }
        return null;
    }
}
```

## 解决 windows 下 java 源码编译乱码

1. 首先确保我们编写的源码文件是 utf-8 编码 
2. 因为 Windows 下 cmd 的默认编码是 `936 (ANSI/OEM - 简体中文 GBK)`，我们也修改为 utf-8, 在打开的 cmd 窗口中直接输入 `chcp 65001`，然后回车即可（临时修改，下次重新打开 cmd ，还是默认的 GBK）
3. 运行 `javac Xxx.java` 就可以了。

** 示例 **

```
E:\workspace\javas>javac demo2\02-map\Main.java
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8

E:\workspace\javas>
E:\workspace\javas>javac -encoding utf-8 demo2\02-map\Main.java
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8

E:\workspace\javas>
```

## 运行 java 字节码

** 示例 **

```
E:\workspace\javas>java -classpath E:\workspace\javas\demo2\02-map Main
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8
测试成功!
测试成功!
测试成功!

E:\workspace\javas>java -cp .;E:\workspace\javas\demo2\02-map Main
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8
测试成功!
测试成功!
测试成功!

E:\workspace\javas>cd demo2\02-map
E:\workspace\javas\demo2\02-map>java Main
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8
测试成功!
测试成功!
测试成功!
```