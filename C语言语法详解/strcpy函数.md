---
title: strcpy函数
date: 2021-06-16 20:31:49
categories: C语言语法详解
---
### 函数原型

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
char *strcpy ( char *dest, const char *src );
```

作用是把`src`所指向的字符串复制到`dest`。该函数返回一个指向目标字符串`dest`的指针。
&emsp;&emsp;注意，目标数组`dest`的大小必须要大于源字符串的长度。

``` cpp
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "This is runoob.com";
    char dest[100] = {0};
    strcpy ( dest, src );
    printf ( "最终的目标字符串：%s\n", dest );
    return ( 0 );
}
```

执行结果：

``` cpp
最终的目标字符串：This is runoob.com
```

### 函数实现

&emsp;&emsp;函数实现如下：

``` cpp
void my_strcpy ( char *s, char *t ) {
    int i = 0;

    while ( ( s[i] = t[i] ) != '\0' ) {
        i++;
    }
}
```