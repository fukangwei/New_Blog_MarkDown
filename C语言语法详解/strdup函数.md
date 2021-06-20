---
title: strdup函数
categories: C语言语法详解
date: 2018-12-10 16:31:53
---
### 函数原型

&emsp;&emsp;`strdup`用于复制出字符串`str`的一个副本，该副本有自己的内存空间：<!--more-->

``` cpp
#include <string.h>
char* strdup ( char* str );
```

函数如果执行成功，则返回指向新字符串的指针，失败则返回`NULL`。

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main ( void ) {
    char string[] = "abcde";
    char* dup_str = NULL;
    dup_str = strdup ( string );
    printf ( "%s\n", dup_str );
    free ( dup_str ); /* 删除在strdup中动态申请的内存 */
    return 0;
}
```

### 函数实现

&emsp;&emsp;具体实现如下：

``` cpp
char* my_strdup ( const char* s ) {
    size_t len = strlen ( s ) + 1;
    void* temp = malloc ( len );

    if ( temp == NULL ) {
        return NULL;
    }

    return ( char * ) memcpy ( temp, s, len );
}
```