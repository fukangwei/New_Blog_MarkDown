---
title: strcat函数
date: 2021-06-16 19:54:09
categories: C语言语法详解
---
### 函数原型

&emsp;&emsp;`strcat`函数用来连接字符串：<!--more-->

``` cpp
char *strcat ( char *dest, const char *src );
```

`strcat`会将参数`src`字符串复制到参数`dest`所指的字符串尾部，`dest`的结束字符`NUL`会被覆盖掉，并在连接后的字符串尾部再增加一个`NUL`。

``` cpp
#include <stdio.h>
#include <string.h>

int main () {
    char str[80];
    strcpy ( str, "these " );
    strcat ( str, "strings " );
    strcat ( str, "are " );
    strcat ( str, "concatenated." );
    printf ( "%s\n", str );
    return 0;
}
```

执行结果如下：

``` cpp
these strings are concatenated.
```

### 具体实现

&emsp;&emsp;具体实现如下：

``` cpp
void my_strcat ( char *s, char *t ) {
    int i = 0, j = 0;

    while ( s[i] != '\0' ) {
        i++;
    }

    while ( ( s[i++] = t[j++] ) != '\0' )
        ;
}
```