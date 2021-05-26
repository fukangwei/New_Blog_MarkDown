---
title: index和rindex函数
categories: C语言语法详解
date: 2018-12-12 06:46:23
---
### index

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <string.h>
char* index ( const char* s, int c );
```

`index`用来找出`s`字符串中字符`c`第一次出现的位置，然后将该字符出现的地址返回。如果没有找到，则返回`NULL`。

``` cpp
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

&emsp;&emsp;函数原型如下：

``` cpp
#include <string.h>
char* rindex ( const char* s, int c );
```

`rindex`用来找出`s`字符串中字符`c`最后一次出现的位置，然后将该字符出现的地址返回。如果没有找到，则返回`NULL`。

``` cpp
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