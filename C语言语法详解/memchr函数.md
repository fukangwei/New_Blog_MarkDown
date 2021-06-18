---
title: memchr函数
date: 2021-06-20 11:35:48
categories: C语言语法详解
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <string.h>
void *memchr ( void *ptr, int value, size_t num );
```

函数作用为从`ptr`所指内存区域的前`num`个字节中查找字符`value`。如果成功，则返回指向字符`value`的指针，否则返回`NULL`。

``` cpp
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char *pch = NULL;
    char str[] = "Example string";
    pch = ( char * ) memchr ( str, 'p', strlen ( str ) );

    if ( pch != NULL ) {
        printf ( "'p' found at position %ld\n", pch - str + 1 );
    } else {
        printf ( "'p' not found.\n" );
    }

    return 0;
}
```

执行结果：

``` cpp
'p' found at position 5
```

&emsp;&emsp;具体实现如下：

``` cpp
void *mymemchar ( void *buffer, int ch, size_t count ) {
    while ( count && ( * ( unsigned char * ) buffer != ( unsigned char ) ch ) ) {
        buffer = ( unsigned char * ) buffer + 1;
        count--;
    }

    return ( count ? ( void * ) buffer : NULL );
}
```

&emsp;&emsp;与`strchr`的区别是，`memchr`检测的是一段内存，`strchr`检测的是一个字符串。如果一段内存中有`0x0`的话，显然不能用`strchr`去查找。