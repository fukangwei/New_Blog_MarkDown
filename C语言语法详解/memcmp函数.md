---
title: memcmp函数
date: 2021-06-20 12:10:18
categories: C语言语法详解
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <string.h>
int memcmp ( const void *buf1, const void *buf2, unsigned int count );
```

`memcmp`比较内存区域`buf1`和`buf2`的前`count`个字节，该函数是按字节比较的。函数返回值如下：

- 当`buf1 < buf2`时，返回值`< 0`。
- 当`buf1 = buf2`时，返回值`= 0`。
- 当`buf1 > buf2`时，返回值`> 0`。

``` cpp
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char buffer1[] = "DWgaOtP12df0";
    char buffer2[] = "DWGAOTP12DF0";
    int n;
    n = memcmp ( buffer1, buffer2, sizeof ( buffer1 ) );

    if ( n > 0 ) {
        printf ( "'%s' is greater than '%s'.\n", buffer1, buffer2 );
    } else if ( n < 0 ) {
        printf ( "'%s' is less than '%s'.\n", buffer1, buffer2 );
    } else {
        printf ( "'%s' is the same as '%s'.\n", buffer1, buffer2 );
    }

    return 0;
}
```

执行结果：

``` cpp
'DWgaOtP12df0' is greater than 'DWGAOTP12DF0'.
```