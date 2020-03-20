---
title: 'CSharp基础'
date: 2020-03-20 16:03:34
categories: C#
---
&emsp;&emsp;`C#`是一个通用的、面向对象的编程语言，它是由`Microsoft`开发的。<!--more-->

### Hello World实例

&emsp;&emsp;一个`C#`程序主要包括以下部分：

- 命名空间声明(`Namespace Declaration`)
- 一个`Class`
- `Class`属性
- `Class`方法
- 一个`Main`方法
- 语句(`Statements`)和表达式(`Expressions`)
- 注释

&emsp;&emsp;如下是一个可以打印出`Hello World`的代码：

``` cs
using System;

namespace HelloWorldApplication {
    class HelloWorld { /* 类名为HelloWorld */
        static void Main(string[] args) { /* Main函数 */
            /* 我的第一个C#程序 */
            Console.WriteLine("Hello World!");
        }
    }
}
```

- 程序的第一行是`using System;`：`using`关键字用于在程序中包含`System`命名空间。一个程序一般有多个`using`语句。
- 下一行是`namespace`声明：一个`namespace`里包含了一系列的类。`HelloWorldApplication`命名空间包含了类`HelloWorld`。
- 下一行是`class`声明：类`HelloWorld`包含了程序使用的数据和方法声明。类一般包含多个方法，方法定义了类的行为。
- 下一行定义了`Main`方法，是所有`C#`程序的`入口点`。`Main`方法说明当程序执行时，类将做什么动作。
- `/*...*/`将会被编译器忽略，它是程序中的`注释`。
- `WriteLine`是一个定义在`System`命名空间中`Console`类的一个方法。该语句会在屏幕上显示消息`Hello World`。

&emsp;&emsp;以下几点值得注意：

- `C#`是大小写敏感的。
- 所有的语句和表达式必须以分号`;`结尾。
- 程序的执行是从`Main`方法开始。
- 与`Java`不同的是，文件名可以不同于类的名称。