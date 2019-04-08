---
title: index和rindex函数
date: 2018-12-12 06:46:23
categories: C语言语法详解
---
&emsp;&emsp;该博客从网上整理了index和rindex函数有关的内容，感谢分享资料的人们！

### index

&emsp;&emsp;函数原型如下所示：

``` c
#include <string.h>
char* index ( const char* s, int c );
```

index用来找出s字符串中字符c第一次出现的位置，然后将该字符出现的地址返回，字符串结束字符(NUL)也视为字符串的一部分。如果没有找到，则返回NULL。

``` c
#include "stdio.h"
#include <stdlib.h>
#include <string.h>

int main() {
    char* s = "0123456789";
    char* p;
    p = index ( s, '5' );
    printf ( "%s\n", p );
}
```

### rindex

&emsp;&emsp;函数原型如下所示：

``` c
#include <string.h>
char* rindex ( const char* s, int c );
```

rindex用来找出s字符串中字符c最后一次出现的位置，然后将该字符出现的地址返回。字符串结束字符(NUL)也视为字符串的一部分。如果没有找到，则返回NULL。

``` c
#include "stdio.h"
#include <stdlib.h>
#include <string.h>

int main() {
    char* s = "01234567890123456789";
    char* p;
    p = rindex ( s, '5' );
    printf ( "%s\n", p );
}
```