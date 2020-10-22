---
title: CSharp可空类型
date: 2020-03-21 07:26:55
categories: C#
---
### 可空类型

&emsp;&emsp;`C#`提供了一个特殊的数据类型，即`nullable`类型(可空类型)，表示可以被赋值为`null`。<!--more-->
&emsp;&emsp;例如，`Nullable<Int32>`读作`可空的 Int32`，可以被赋值为`-2,147,483,648`到`2,147,483,647`之间的任意值，也可以被赋值为`null`值。类似的，`Nullable<bool>`变量可以被赋值为`true`、`false`或`null`。
&emsp;&emsp;在处理数据库和其他包含可能未赋值的元素的数据类型时，将`null`赋值给数值类型或布尔型的功能特别有用。例如，数据库中的布尔型字段可以存储值`true`或`false`，或者该字段也可以未定义。
&emsp;&emsp;声明一个`nullable`类型的语法如下：

``` cs
<data_type> ? <variable_name> = null;
```

&emsp;&emsp;下面的实例演示了可空数据类型的用法：

``` cs
using System;

namespace CalculatorApplication {
    class NullablesAtShow {
        static void Main(string[] args) {
            int? num1 = null;
            int? num2 = 45;
            double? num3 = new double?();
            double? num4 = 3.14157;

            bool? boolval = new bool?();

            Console.WriteLine("显示可空类型的值：{0}, {1}, {2}, {3}",
                               num1, num2, num3, num4);
            Console.WriteLine("一个可空的布尔值：{0}", boolval);
        }
    }
}
```

执行结果：

``` cs
显示可空类型的值： , 45,  , 3.14157
一个可空的布尔值：
```

### 单问号与双问号

- `?`：单问号用于对`int`、`double`、`bool`等无法直接赋值为`null`的数据类型进行`null`的赋值，意思是这个数据类型是`Nullable`类型的。<!--more-->

``` cs
int? i = 3;
// 等同于
Nullable<int> i = new Nullable<int>(3);
/*--------------------*/
int i; // 默认值0
int? ii; // 默认值null
```

- `??`: 双问号可用于判断一个变量在为`null`时返回一个指定的值。

``` cs
using System;

namespace CalculatorApplication {
    class NullablesAtShow {
        static void Main(string[] args) {
            double? num1 = null;
            double? num2 = 3.14157;
            double num3;
            num3 = num1 ?? 5.34; // num1如果为空值，则返回5.34
            Console.WriteLine("num3 的值：{0}", num3);
            num3 = num2 ?? 5.34;
            Console.WriteLine("num3 的值：{0}", num3);
        }
    }
}
```

执行结果：

``` cs
num3 的值：5.34
num3 的值：3.14157
```