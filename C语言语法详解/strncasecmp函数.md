---
title: strncasecmp函数
date: 2021-03-09 07:12:59
categories: C语言语法详解
---
&emsp;&emsp;`strncasecmp`原型如下：

``` cpp
int strncasecmp ( const char *s1, const char *s2, size_t n );
```

它用来比较字符串`s1`和`s2`的前`n`个字符，在比较时会自动忽略大小写的差异：

1. 若`s1`和`s2`相同，则返回`0`。
2. 若`s1`大于`s2`，则返回大于`0`的值。
3. 若`s1`小于`s2`，则返回小于`0`的值。

``` cpp
#include <string.h>
#include <stdio.h>

int main() {
    char *a = "aBcDeF";
    char *b = "AbCdEf";

    if ( !strncasecmp ( a, b, 3 ) ) {
        printf ( "%s = %s\n", a, b );
    }

    return 0;
}
```

执行结果：

``` cpp
aBcDeF = AbCdEf
```