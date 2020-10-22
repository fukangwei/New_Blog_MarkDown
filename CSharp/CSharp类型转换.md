---
title: CSharp类型转换
date: 2020-03-20 17:55:06
categories: C#
---
&emsp;&emsp;类型转换就是把数据从一种类型转换为另一种类型。在`C#`中，类型转换有`2`种形式：<!--more-->

- **隐式类型转换**：这些转换是`C#`默认的以安全方式进行的转换，不会导致数据丢失。例如，从小的整数类型转换为大的整数类型，从派生类转换为基类。
- **显式类型转换**：强制类型转换。显式转换需要强制转换运算符，而且强制转换可能会造成数据丢失。

``` cs
using System;

namespace TypeConversionApplication {
    class ExplicitConversion {
        static void Main(string[] args) {
            double d = 5673.74;
            int i;

            i = (int)d; // 强制转换double为int
            Console.WriteLine(i); // 输出5673
        }
    }
}
```

### C#类型转换方法

&emsp;&emsp;`C#`提供了下列内置的类型转换方法：

- `ToBoolean`：如果可能的话，把类型转换为布尔型。
- `ToByte`：把类型转换为字节类型。
- `ToChar`：如果可能的话，把类型转换为单个`Unicode`字符类型。
- `ToDateTime`：把类型(整数或字符串类型)转换为`日期-时间`结构。
- `ToDecimal`：把浮点型或整数类型转换为十进制类型。
- `ToDouble`：把类型转换为双精度浮点型。
- `ToInt16`：把类型转换为`16`位整数类型。
- `ToInt32`：把类型转换为`32`位整数类型。
- `ToInt64`：把类型转换为`64`位整数类型。
- `ToSbyte`：把类型转换为有符号字节类型。
- `ToSingle`：把类型转换为单精度浮点数类型。
- `ToString`：把类型转换为字符串类型。
- `ToUInt16`：把类型转换为`16`位无符号整数类型。
- `ToUInt32`：把类型转换为`32`位无符号整数类型。
- `ToUInt64`：把类型转换为`64`位无符号整数类型。

``` cs
using System;

namespace TypeConversionApplication {
    class StringConversion {
        static void Main(string[] args) {
            int i = 75;
            float f = 53.005f;
            double d = 2345.7652;
            bool b = true;

            Console.WriteLine(i.ToString()); // 输出75
            Console.WriteLine(f.ToString()); // 输出53.005
            Console.WriteLine(d.ToString()); // 输出2345.7652
            Console.WriteLine(b.ToString()); // 输出True
        }
    }
}
```