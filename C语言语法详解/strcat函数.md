---
title: strcat函数
date: 2021-06-16 19:54:09
categories: C语言语法详解
---
&emsp;&emsp;`strcat`函数是将字符串`t`连接到字符串`s`的尾部，注意`s`必须有足够大的空间：<!--more-->

``` cpp
void strcat ( char s[], char t[] ) {
    int i, j;
    i = j = 0;

    while ( s[i] != '\0' ) { /* 判断是否为字符串s的尾部 */
        i++;
    }

    while ( ( s[i++] = t[j++] ) != '\0' ) /* 拷贝t */
        ;
}
```