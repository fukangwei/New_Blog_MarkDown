---
title: strlen函数
date: 2021-06-16 20:19:27
categories: C语言语法详解
---
### 函数原型

&emsp;&emsp;函数原型如下：

``` cpp
size_t strlen ( const char *str );
```

该函数用于计算字符串`str`的长度。

``` cpp
#include <stdio.h>
#include <string.h>

int main () {
    char str[] = "This is runoob.com";
    int len = 0;
    len = strlen ( str );
    printf ( "%s的长度是%d\n", str, len );
    return ( 0 );
}
```

执行结果：

``` cpp
This is runoob.com的长度是18
```

### 函数实现

&emsp;&emsp;具体实现如下：<!--more-->

``` cpp
int my_strlen ( char *s ) {
    int n;

    for ( n = 0; *s != '\0'; s++ ) {
        n++;
    }

    return n;
}
```

&emsp;&emsp;递归方式如下：

``` cpp
int my_strlen ( char *s ) {
    if ( *s++ ) {
        return my_strlen ( s ) + 1;
    }

    return 0;
}
```