---
title: strlen函数
date: 2021-06-16 20:19:27
categories: C语言语法详解
---
&emsp;&emsp;`strlen`函数返回字符串`s`的长度：<!--more-->

``` cpp
int strlen ( char *s ) {
    int n;

    for ( n = 0; *s != '\0', s++ ) {
        n++;
    }

    return n;
}
```

不使用额外变量的方式：

``` cpp
int mystrlen(char *s) {
    if (*s++) {
        return mystrlen(s) + 1;
    }

    return 0;
}
```