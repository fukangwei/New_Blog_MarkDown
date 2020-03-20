---
title: CSharp数据类型
date: 2020-03-20 17:13:58
categories: C#
---
&emsp;&emsp;在`C#`中，变量分为以下几种类型：<!--more-->

- 值类型(`Value Types`)
- 引用类型(`Reference Types`)
- 指针类型(`Pointer Types`)

### 值类型

&emsp;&emsp;值类型的变量可以直接包含数据。比如`int`、`char`、`float`，它们分别存储数字、字符、浮点数。当你声明一个`int`类型时，系统分配内存来存储值。下表列出了`C#`中可用的值类型：

类型      | 描述                 | 默认值
----------|---------------------|-------
`bool`    | 布尔值               | `False`
`byte`    | `8`位无符号整数      | `0`
`char`    | `16`位`Unicode`字符  | `'\0'`
`decimal` | `128`位精确的十进制值 | `0.0M`
`double`  | `64`位双精度浮点型    | `0.0D`
`float`   | `32`位单精度浮点型    | `0.0F`
`int`     | `32`位有符号整数类型  | `0`
`long`    | `64`位有符号整数类型  | `0L`
`sbyte`   | `8`位有符号整数类型   | `0`
`short`   | `16`位有符号整数类型  | `0`
`uint`    | `32`位无符号整数类型  | `0`
`ulong`   | `64`位无符号整数类型  | `0`
`ushort`  | `16`位无符号整数类型  | `0`

&emsp;&emsp;如果想要得到一个类型或一个变量在特定平台上的准确尺寸，可以使用`sizeof`方法，以`byte`为单位。

``` cs
using System;

namespace DataTypeApplication {
    class Program {
        static void Main(string[] args) {
            Console.WriteLine("Size of int: {0}", sizeof(int));
            Console.ReadLine();
        }
    }
}
```

执行结果：

``` cs
Size of int: 4
```

### 引用类型

&emsp;&emsp;引用类型不包含存储在变量中的实际数据，但它们包含对变量的引用。换句话说，它们指的是一个内存位置。内置的引用类型有：`Object`、`Dynamic`和`String`。

#### 对象类型(Object)

&emsp;&emsp;对象类型是`C#`通用类型系统(`Common Type System`，`CTS`)中所有数据类型的终极基类。`Object`是`System.Object`类的别名。所以对象类型可以被分配任何其他类型(`值类型`、`引用类型`、`预定义类型`或者`用户自定义类型`)的值。但是，在分配值之前，需要先进行类型转换。
&emsp;&emsp;当一个值类型转换为对象类型时，则被称为`装箱`；另一方面，当一个对象类型转换为值类型时，则被称为`拆箱`。
&emsp;&emsp;装箱实例如下：

``` cs
int val = 8;
object obj = val; // 整型数据转换为了对象类型
```

&emsp;&emsp;拆箱实例如下，只有装过箱的数据才能拆箱：

``` cs
int val = 8;
object obj = val; // 先装箱
int nval = (int)obj; // 再拆箱
```

#### 动态类型(Dynamic)

&emsp;&emsp;你可以存储任何类型的值在动态数据类型变量中。这些变量的类型检查是在运行时发生的。声明动态类型变量的语法如下：

``` cs
dynamic <variable_name> = value;
```

例如：

``` cs
dynamic d = 20;
```

&emsp;&emsp;动态类型与对象类型相似，但是对象类型变量的类型检查是在编译时发生的，而动态类型变量的类型检查是在运行时发生的。

#### 字符串类型(String)

&emsp;&emsp;字符串类型允许你给变量分配任何字符串值。字符串类型是`System.String`类的别名，它是从对象类型派生的。
&emsp;&emsp;字符串类型的值可以通过两种形式进行分配：

- `"`方式如下：

``` cs
String str = "runoob.com";
```

- `@`方式如下：

``` cs
@"runoob.com";
```

&emsp;&emsp;在`C#`中，`string`字符串的前面可以加`@`，将转义字符当作普通字符对待：

``` cs
string str = @"C:\Windows";
```

等价于：

``` cs
string str = "C:\\Windows";
```

&emsp;&emsp;`@`字符串中可以任意换行，换行符以及缩进空格都计算在字符串长度之中。

``` cs
string str = @"<script type=""text/javascript"">
    <!--
    -->
</script>";
```

### 指针类型

&emsp;&emsp;指针类型变量存储另一种类型的内存地址。`C#`中的指针与`C/C++`中的指针有相同的功能。声明指针类型的语法如下：

``` cs
type* identifier;
```

例如：

``` cs
char* cptr;
int* iptr;
```