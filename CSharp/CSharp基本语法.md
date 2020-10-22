---
title: CSharp基本语法
date: 2020-03-20 16:53:36
categories: C#
---
&emsp;&emsp;`C#`是一种面向对象的编程语言。在面向对象的程序设计方法中，程序是由各种对象组成。相同种类的对象通常具有相同的类型，或者说是在相同的`class`中。<!--more-->
&emsp;&emsp;以`Rectangle`为例，它具有`length`和`width`属性。根据设计，它可能具有接受这些属性值、计算面积和显示细节等功能。

``` cs
using System;

namespace RectangleApplication {
    class Rectangle {
        /* 成员变量 */
        double length;
        double width;
        public void Acceptdetails() {
            length = 4.5;
            width = 3.5;
        }

        public double GetArea() {
            return length * width;
        }

        public void Display() {
            Console.WriteLine("Length: {0}", length);
            Console.WriteLine("Width: {0}", width);
            Console.WriteLine("Area: {0}", GetArea());
        }
    }

    class ExecuteRectangle {
        static void Main(string[] args) {
            Rectangle r = new Rectangle();
            r.Acceptdetails();
            r.Display();
        }
    }
}
```

执行结果：

``` cs
Length: 4.5
Width: 3.5
Area: 15.75
```

### using关键字

&emsp;&emsp;在任何`C#`程序中，第一条语句都是：

``` charp
using System;
```

`using`关键字用于在程序中包含命名空间。一个程序可以包含多个`using`语句。

### class关键字

&emsp;&emsp;`class`关键字用于声明一个类。

### C#中的注释

&emsp;&emsp;注释用于解释代码，编译器对注释进行忽略。在`C#`程序中，多行注释以`/*`开始，并以字符`*/`终止：

``` cs
/* This program demonstrates.
   The basic syntax of C# programming
   Language */
```

单行注释使用`//`符号：

``` cs
// end class Rectangle
```

### 成员变量

&emsp;&emsp;变量是类的属性或数据成员，用于存储数据。在上面的程序中，类`Rectangle`有两个成员变量，名为`length`和`width`。

### 成员函数

&emsp;&emsp;函数是一系列执行指定任务的语句，类的成员函数是在类内声明的。类`Rectangle`包含了三个成员函数，即`AcceptDetails`、`GetArea`和`Display`。

### 实例化一个类

&emsp;&emsp;在上面的程序中，类`ExecuteRectangle`是一个包含`Main`方法和实例化类`Rectangle`的类。

### 标识符

&emsp;&emsp;标识符是用来识别`类`、`变量`、`函数`或任何其它`用户自定义的项目`。在`C#`中，类的命名必须遵循如下基本规则：

- 标识符必须以`字母`、`下划线`或`@`开头，后面可以跟一系列的字母、数字(`0-9`)、下划线(`_`)、`@`。
- 标识符中的第一个字符不能是数字。
- 标识符不能是`C#`关键字，除非它们有一个`@`前缀。例如，`@if`是有效的标识符，但`if`不是。
- 标识符必须区分大小写。
- 不能与`C#`的类库名称相同。

### C#关键字

&emsp;&emsp;`C#`编译器中存在预定义的`保留关键字`(`Reserved Keywords`)，这些关键字不能用作标识符，例如`if`和`switch`。但是，如果你想使用这些关键字作为标识符，可以在关键字前面加上`@`字符。
&emsp;&emsp;在`C#`中，有些关键字在代码的上下文中有特殊的意义，如`get`和`set`，这些被称为`上下文关键字`(`Contextual Keywords`)。