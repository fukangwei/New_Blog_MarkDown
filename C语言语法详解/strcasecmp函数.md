---
title: strcasecmp函数
date: 2018-12-10 20:31:20
categories: C语言语法详解
---
&emsp;&emsp;`strcasecmp`用来比较参数`s1`和`s2`字符串，比较时会自动忽略大小写的差异：

``` cpp
#include <string.h>
int strcasecmp ( const char* s1, const char* s2 );
```

- 若参数`s1`和`s2`字符串相同，则返回`0`。
- 若`s1`的长度大于`s2`的长度，则返回大于`0`的值。
- 若`s1`的长度小于`s2`的长度，则返回小于`0`的值。

``` cpp
#include <string.h>

int main ( void ) {
    char* a = "aBcDeF";
    char* b = "AbCdEf";

    if ( !strcasecmp ( a, b ) ) {
        printf ( "%s = %s\n", a, b );
    }
}
```

执行结果：

``` bash
aBcDeF = AbCdEf
```