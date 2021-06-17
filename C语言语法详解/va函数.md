---
title: va函数
categories: C语言语法详解
date: 2018-12-10 09:08:20
---
&emsp;&emsp;`va_list`、`va_start`、`va_arg`和`va_end`这几个宏都是用于可变参数的函数。<!--more-->

- `va_list`：声明一个指向参数列表的字符型指针变量。
- `va_start ( ap, v )`：

1. 参数`ap`是指向可变参数字符串的变量。
2. 参数`v`用于指定可变参数列表中参数的个数。

- `va_arg ( ap, t )`：

1. 参数`ap`是指向可变参数字符串的变量。
2. 参数`t`是可变参数的类型。

- `va_end ( ap )`用于将指向可变参数字符串的变量清空。

&emsp;&emsp;如下是用可变参数列表的函数来求数组和的代码：

``` cpp
#include <stdio.h>
#include <stdarg.h>
#include <stdlib.h>

int sum ( int number, ... ) { /* 第一个参数指定了参数的个数 */
    va_list vaptr;
    int i;
    int sum = 0;
    va_start ( vaptr, number );

    for ( i = 0; i < number; i++ ) {
        sum += va_arg ( vaptr, int );
    }

    va_end ( vaptr );
    return sum;
}

int main ( void ) {
    printf ( "%d\n", sum ( 4, 4, 3, 2, 1 ) );
    return 0;
}
```