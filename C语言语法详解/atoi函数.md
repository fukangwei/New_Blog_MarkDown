---
title: atoi函数
date: 2021-06-16 20:52:56
categories: C语言语法详解
---
&emsp;&emsp;`atoi`函数是将字符串转换为相应的整型数。<!--more-->
&emsp;&emsp;版本`1`如下：

``` cpp
int atoi ( char *str ) {
    int value = 0;

    while ( *str >= '0' && *str <= '9' ) {
        value *= 10;
        value += *str - '0';
        str++;
    }

    return value;
}
```

&emsp;&emsp;版本`2`如下：

``` cpp
#include <ctype.h>

int atoi ( char s[] ) {
    int i, n, sign;

    for ( i = 0; isspace ( s[i] ); i++ ) /* skip white space */
        ;

    sign = ( s[i] == '-' ) ? -1 : 1;

    if ( s[i] == '+' || s[i] == '-' ) { /* skip sign */
        i++;
    }

    for ( n = 0; isdigit ( s[i] ); i++ ) {
        n = 10 * n + ( s[i] - '0' );
    }

    return sign * n;
}
```