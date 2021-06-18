---
title: atof函数
date: 2021-06-16 21:06:30
categories: C语言语法详解
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
double atof ( const char *str );
```

函数作用是把参数`str`所指向的字符串转换为一个浮点数。如果没有执行有效的转换，则返回`0.0`。

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    float val;
    char str[20];
    strcpy ( str, "98993489" );
    val = atof ( str );
    printf ( "str = %s, float = %f\n", str, val );
    strcpy ( str, "runoob" );
    val = atof ( str );
    printf ( "str = %s, float = %f\n", str, val );
    return ( 0 );
}
```

执行结果：

``` cpp
str = 98993489, float = 98993488.000000
str = runoob, float = 0.000000
```

&emsp;&emsp;`atof`的具体实现如下：

``` cpp
#include <ctype.h>

double my_atof ( char s[] ) {
    double val, power;
    int i, sign;

    for ( i = 0; isspace ( s[i] ); i++ ) /* skip white space */
        ;

    sign = ( s[i] == '-' ) ? -1 : 1;

    if ( s[i] == '+' || s[i] == '-' ) {
        i++;
    }

    for ( val = 0.0; isdigit ( s[i] ); i++ ) {
        val = 10.0 * val + ( s[i] - '0' );
    }

    if ( s[i] == '.' ) {
        i++;
    }

    for ( power = 1.0; isdigit ( s[i] ); i++ ) {
        val = 10.0 * val + ( s[i] - '0' );
        power *= 10;
    }

    return sign * val / power;
}
```