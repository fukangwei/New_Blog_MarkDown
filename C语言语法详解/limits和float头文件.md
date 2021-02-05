---
title: limits和float头文件
categories: C语言语法详解
date: 2018-12-11 16:32:45
---
&emsp;&emsp;`limits.h`专门用于检测整型数据数据类型的表达值范围，主要提供对整型和字符型范围的限制的宏，同样没有指定类型和函数的定义。<!--more-->

宏          | 值            | 描述
------------|---------------|-----
`CHAR_BIT`  | `8`           | 定义了字节中的比特数
`SCHAR_MIN` | `-127`        | 定义有符号`char`类型的最小值
`SCHAR_MAX` | `127`         | 定义有符号`char`类型的最大值
`UCHAR_MAX` | `255`         | 定义`unsigned char`类型的最大值
`CHAR_MIN`  | `0`           | 定义`char`类型的最小值(`0`或者`SCHAR_MIN`)
`CHAR_MAX`  | `127`         | 定义`char`类型的最大值(`UCHAR_MAX`或者`SCHAR_MAX`)
`SHRT_MIN`  | `-32767`      | 定义短整型的最小值
`SHRT_MAX`  | `+32767`      | 定义短整型的最大值
`USHRT_MAX` | `65535`       | 定义无符号短整型的最大值
`INT_MIN`   | `-32767`      | 定义`int`的最小值
`INT_MAX`   | `+32767`      | 定义`int`的最大值
`UINT_MAX`  | `65535`       | 定义`unsigned int`的最大值
`LONG_MIN`  | `-2147483647` | 定义长整型的最小值
`LONG_MAX`  | `+2147483647` | 定义长整型的最大值
`ULONG_MAX` | `4294967295`  | 定义无符号长整型的最大值

示例如下：

``` cpp
#include <stdio.h>
#include <limits.h>

int main ( void ) {
    printf ( "The number of bits in a byte %d\n", CHAR_BIT );
    printf ( "The minimum value of SIGNED CHAR = %d\n", SCHAR_MIN );
    printf ( "The maximum value of SIGNED CHAR = %d\n", SCHAR_MAX );
    printf ( "The maximum value of UNSIGNED CHAR = %d\n", UCHAR_MAX );
    printf ( "The minimum value of SHORT INT = %d\n", SHRT_MIN );
    printf ( "The maximum value of SHORT INT = %d\n", SHRT_MAX );
    printf ( "The minimum value of INT = %d\n", INT_MIN );
    printf ( "The maximum value of INT = %d\n", INT_MAX );
    printf ( "The minimum value of CHAR = %d\n", CHAR_MIN );
    printf ( "The maximum value of CHAR = %d\n", CHAR_MAX );
    printf ( "The minimum value of LONG = %ld\n", LONG_MIN );
    printf ( "The maximum value of LONG = %ld\n", LONG_MAX );
    return ( 0 );
}
```

执行结果：

``` bash
The number of bits in a byte 8
The minimum value of SIGNED CHAR = -128
The maximum value of SIGNED CHAR = 127
The maximum value of UNSIGNED CHAR = 255
The minimum value of SHORT INT = -32768
The maximum value of SHORT INT = 32767
The minimum value of INT = -32768
The maximum value of INT = 32767
The minimum value of CHAR = -128
The maximum value of CHAR = 127
The minimum value of LONG = -2147483648
The maximum value of LONG = 2147483647
```

&emsp;&emsp;`float.h`提供了浮点型的范围和精度的宏，没有类型和函数的定义，一般用于数值分析。`float.h`头文件包含了一组与浮点值相关的依赖于平台的常量。这些常量是由`ANSI C`提出的，这让程序更具有可移植性。在讲解这些常量之前，最好先弄清楚浮点数是由下面四个元素组成的：

组件 | 组件描述
-----|-----------
`S`  | 符号(`+`或`-`)
`b`  | 指数表示的基数，`2`表示二进制，`10`表示十进制，`16`表示十六进制等等
`e`  | 指数，一个介于最小值`emin`和最大值`emax`之间的整数
`p`  | 精度，基数`b`的有效位数

基于以上`4`个组成部分，一个浮点数的值如下：

``` bash
floating-point = (S) p * b^e
```

或

``` bash
floating-point = (+/-) precision * base^exponent
```

&emsp;&emsp;下面的值是特定实现的，并且是通过`define`指令来定义的，这些值都不得低于下边所给出的值。请注意，所有的实例`FLT`是指类型`float`，`DBL`是指类型`double`，`LDBL`是指类型`long double`。

宏 | 描述
---|----
FLT_ROUNDS | 定义浮点加法的舍入模式，它可以是下列任何一个值：<li>-1：无法确定</li> <li>0：趋向于零</li> <li>1：去最近的值</li><li>2：趋向于正无穷</li><li>3：趋向于负无穷</li>
FLT_RADIX 2 | 这个宏定义了指数表示的基数，基数`2`表示二进制，基数`10`表示十进制，基数`16`表示十六进制
<li>FLT_MANT_DIG</li> <li>DBL_MANT_DIG</li> <li>LDBL_MANT_DIG</li> | 这些宏定义了`FLT_RADIX`基数中的位数
<li>FLT_DIG 6</li> <li>DBL_DIG 10</li> <li>LDBL_DIG 10</li> | 这些宏定义了舍入后不会改变表示的十进制数字的最大值(基数`10`)
<li>FLT_MIN_EXP</li> <li>DBL_MIN_EXP</li> <li>LDBL_MIN_EXP</li> | 这些宏定义了基数为`FLT_RADIX`时的指数的最小负整数值
<li>FLT_MIN_10_EXP -37</li> <li>DBL_MIN_10_EXP -37</li> <li>LDBL_MIN_10_EXP -37</li> | 这些宏定义了基数为`10`时的指数的最小负整数值
<li>FLT_MAX_EXP</li> <li>DBL_MAX_EXP</li> <li>LDBL_MAX_EXP</li> | 这些宏定义了基数为`FLT_RADIX`时的指数的最大整数值
<li>FLT_MAX_10_EXP +37</li> <li>DBL_MAX_10_EXP +37</li> <li>LDBL_MAX_10_EXP +37</li> | 这些宏定义了基数为`10`时的指数的最大整数值
<li>FLT_MAX 1E+37</li> <li>DBL_MAX 1E+37</li> <li>LDBL_MAX 1E+37</li> | 这些宏定义最大的有限浮点值
<li>FLT_EPSILON 1E-5</li> <li>DBL_EPSILON 1E-9</li> <li>LDBL_EPSILON 1E-9</li> | 这些宏定义了可表示的最小有效数字
<li>FLT_MIN 1E-37</li> <li>DBL_MIN 1E-37</li> <li>LDBL_MIN 1E-37</li> | 这些宏定义了最小的浮点值

&emsp;&emsp;下面的实例演示了`float.h`文件中定义的一些常量的使用：

``` cpp
#include <stdio.h>
#include <float.h>

int main() {
    printf ( "The maximum value of float = %.10e\n", FLT_MAX );
    printf ( "The minimum value of float = %.10e\n", FLT_MIN );
    printf ( "The number of digits in the number = %.10e\n", FLT_MANT_DIG );
}
```

执行结果：

``` bash
The maximum value of float = 3.4028234664e+38
The minimum value of float = 1.1754943508e-38
The number of digits in the number = 7.2996655210e-312
```