---
title: strcmp函数
date: 2021-06-16 20:44:16
categories: C语言语法详解
---
&emsp;&emsp;`strcmp`函数如下：<!--more-->

``` cpp
int strcmp ( char *s, char *t ) {
    for ( ; *s == *t; s++, t++ )
        if ( *s == '\0' ) {
            return 0;
        }

    return *s - *t;
}
```