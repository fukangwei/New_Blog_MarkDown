---
title: atof函数
date: 2021-06-16 21:06:30
categories: C语言语法详解
---
&emsp;&emsp;`atof`函数把字符串转换为相应的双精度浮点数：<!--more-->

``` cpp
#include <ctype.h>

double atof ( char s[] ) {
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