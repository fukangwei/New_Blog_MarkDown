---
title: strcmp函数
date: 2021-06-16 20:44:16
categories: C语言语法详解
---
### 函数原型

&emsp;&emsp;`strcmp`函数用于对两个字符串进行比较：<!--more-->

``` cpp
#include <string.h>
int strcmp ( const char *s1, const char *s2 );
```

`strcmp`会根据`ASCII`码依次比较`s1`和`s2`的每一个字符，直到出现不相等的字符，或者到达字符串末尾。

- 如果函数返回值小于`0`，则表示`s1`小于`s2`。
- 如果函数返回值大于`0`，则表示`s1`大于`s1`。
- 如果函数返回值等于`0`，则表示`s1`等于`s2`。

&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <string.h>

int main () {
    char str1[15];
    char str2[15];
    int ret;
    strcpy ( str1, "abcdef" );
    strcpy ( str2, "ABCDEF" );
    ret = strcmp ( str1, str2 );

    if ( ret < 0 ) {
        printf ( "str1小于str2\n" );
    } else if ( ret > 0 ) {
        printf ( "str1大于str2\n" );
    } else {
        printf ( "str1等于str2\n" );
    }

    return 0;
}
```

执行结果：

``` cpp
str1大于str2
```

### 函数实现

&emsp;&emsp;函数实现如下：

``` cpp
int my_strcmp ( char *s, char *t ) {
    for ( ; *s == *t; s++, t++ )
        if ( *s == '\0' ) {
            return 0;
        }

    return *s - *t;
}
```