---
title: strcpy函数
date: 2021-06-16 20:31:49
categories: C语言语法详解
---
&emsp;&emsp;`strcpy`函数将指针`t`指向的字符串复制到指针`s`指向的位置：<!--more-->

``` cpp
void strcpy ( char *s, char *t ) {
    int i;
    i = 0;

    while ( ( s[i] = t[i] ) != '\0' ) {
        i++;
    }
}
```