---
title: atoi函数
date: 2021-06-16 20:52:56
categories: C语言语法详解
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
int atoi ( const char *str );
```

函数把参数`str`所指向的字符串转换为一个整数，如果没有执行有效的转换，则返回`0`。

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    int val;
    char str[20];
    strcpy ( str, "98993489" );
    val = atoi ( str );
    printf ( "str = %s, int = %d\n", str, val );
    strcpy ( str, "runoob.com" );
    val = atoi ( str );
    printf ( "str = %s, int = %d\n", str, val );
    return ( 0 );
}
```

执行结果：

``` cpp
str = 98993489, int = 98993489
str = runoob.com, int = 0
```

&emsp;&emsp;`atoi`的具体实现如下：
&emsp;&emsp;版本`1`：

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

&emsp;&emsp;版本`2`：

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