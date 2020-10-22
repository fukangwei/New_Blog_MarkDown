---
title: CSharp枚举
date: 2020-03-21 11:11:18
categories: C#
---
&emsp;&emsp;枚举是一组命名整型常量，枚举类型是使用`enum`关键字声明的。`C#`枚举是值类型，因此不能继承或传递继承。<!--more-->
&emsp;&emsp;声明枚举的一般语法如下：

``` cs
enum <enum_name> {
    enumeration list
};
```

- `enum_name`：指定枚举的类型名称。
- `enumeration list`：是一个用逗号`,`分隔的标识符列表。

&emsp;&emsp;枚举列表中的每个符号代表一个整数值，一个比它前面的符号大的整数值。默认情况下，第一个枚举符号的值是`0`。第`n`个符号值与第`n - 1`个有关。

``` cs
enum Days {Sun, Mon, tue, Wed, thu, Fri, Sat};
```

&emsp;&emsp;示例如下：

``` cs
using System;

public class EnumTest {
    enum Day {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

    static void Main() {
        int x = (int)Day.Sun;
        int y = (int)Day.Fri;
        Console.WriteLine("Sun = {0}", x);
        Console.WriteLine("Fri = {0}", y);
    }
}
```

执行结果：

``` cs
Sun = 0
Fri = 5
```

&emsp;&emsp;实际上，你也可以自定义每个符号的值：

``` cs
using System;

class Program {
    enum Day {a = 8, b, c = 1, e, f, g};

    static void Main(string[] args) {
        int a = (int)Day.a;
        int e = (int)Day.e;
        Console.WriteLine("a的值是：{0}", a);
        Console.WriteLine("e的值是：{0}", e);
    }
}
```

执行结果：

``` cs
a的值是：8
e的值是：2
```