---
title: CSharp变量
date: 2020-03-20 18:52:19
categories: C#
---
&emsp;&emsp;一个变量只不过是一个供程序操作的存储区的名字。在`C#`中，每个变量都有一个特定的类型，类型决定了变量的内存大小和布局。范围内的值可以存储在内存中，可以对变量进行一系列操作。<!--more-->
&emsp;&emsp;`C#`基本的值类型大致分为以下几类：

类型      | 举例
----------|-----
整数类型   | `sbyte`、`byte`、`short`、`ushort`、`int`、`uint`、`long`、`ulong`和`char`
浮点型     | `float`和`double`
十进制类型 | `decimal`
布尔类型   | `true`或`false`
空类型     | 可为`空值`的数据类型

&emsp;&emsp;`C#`允许定义其他值类型的变量，比如`enum`；也允许定义引用类型变量，比如`class`。

### 变量定义

&emsp;&emsp;变量定义的语法如下：

``` cs
<data_type> <variable_list>;
```

在这里，`data_type`必须是一个有效的`C#`数据类型，可以是`char`、`int`、`float`、`double`或其他用户自定义的数据类型。`variable_list`可以由一个或多个用逗号`,`分隔的标识符名称组成。
&emsp;&emsp;一些有效的变量定义如下：

``` cs
int i, j, k;
char c, ch;
float f, salary;
double d;
```

你可以在变量定义时进行初始化：

``` cs
int i = 100;
```

### 变量初始化

&emsp;&emsp;变量通过在等号后跟一个常量表达式进行初始化。初始化的一般形式为：

``` cs
variable_name = value;
```

变量可以在声明时被初始化(指定一个初始值)：

``` cs
<data_type> <variable_name> = value;
```

实例如下：

``` cs
int d = 3, f = 5;    // 初始化d和f
byte z = 22;         // 初始化z
double pi = 3.14159; // 声明pi的近似值
char x = 'x';        // 变量x的值为'x'
```

代码示例如下：

``` cs
using System;

namespace VariableDefinition {
    class Program {
        static void Main(string[] args) {
            short a;
            int b;
            double c;

            /* 实际初始化 */
            a = 10;
            b = 20;
            c = a + b;
            // 输出“a = 10, b = 20, c = 30”
            Console.WriteLine("a = {0}, b = {1}, c = {2}", a, b, c);
            Console.ReadLine();
        }
    }
}
```

### 接收来自用户的值

&emsp;&emsp;`System`命名空间中的`Console`类提供了一个函数`ReadLine`，用于接收来自用户的输入，并把它存储到一个变量中。

``` cs
int num;
num = Convert.ToInt32(Console.ReadLine());
```

&emsp;&emsp;函数`Convert.ToInt32`把用户输入的数据转换为`int`数据类型，因为`Console.ReadLine`只接收字符串格式的数据。