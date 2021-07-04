---
title: C数据类型
categories: C语言语法详解
date: 2019-10-01 21:57:29
---
### 整数类型

&emsp;&emsp;下表列出了关于标准整数类型的存储大小：<!--more-->

类型             | 存储大小     | 类型             | 存储大小
-----------------|-------------|------------------|---------
`char`           | `1`字节      | `unsigned char` | `1`字节
`signed char`    | `1`字节      | `int`           | `2`或`4`字节
`unsigned int`   | `2`或`4`字节 | `short`         | `2`字节
`unsigned short` | `2`字节      | `long`          | `4`字节
`unsigned long`  | `4`字节

### sizeof

&emsp;&emsp;为了得到某个类型或变量的准确大小，可以使用`sizeof`运算符：

``` cpp
#include <stdio.h>

int main() {
    printf ( "int存储大小：%ld\n", sizeof ( int ) );
    return 0;
}
```

执行结果：

``` cpp
int存储大小：4
```

### 浮点类型

&emsp;&emsp;下表列出了关于标准浮点类型的存储大小和精度的细节：

类型          | 存储大小 | 精度
--------------|---------|--------
`float`       | `4`字节  | `6`位小数
`double`      | `8`字节  | `15`位小数
`long double` | `16`字节 | `19`位小数

&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <float.h>

int main() {
    printf ( "float存储最大字节数：%lu\n", sizeof ( float ) );
    printf ( "float最小值：%E\n", FLT_MIN );
    printf ( "float最大值：%E\n", FLT_MAX );
    printf ( "精度值：%d\n", FLT_DIG );
    return 0;
}
```

执行结果：

``` cpp
float存储最大字节数：4
float最小值：1.175494E-38
float最大值：3.402823E+38
精度值：6
```