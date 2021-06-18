---
title: snprintf和sprintf
categories: C语言语法详解
date: 2018-12-06 17:49:34
---
### sprintf函数

&emsp;&emsp;函数原型如下：

``` cpp
int sprintf ( char* str, const char* format, ... );
```

函数的作用为格式化输出到字符串`str`中。如果函数执行成功，则返回`str`指向的字符串的长度，失败则返回`-1`。

``` cpp
#include <stdio.h>

int main ( void ) {
    int tmp = 10;
    char cstr[20];
    int length = sprintf ( cstr, "%d * %d = %d", tmp, tmp, tmp * tmp );
    printf ( "str is %s, length is %d\n", cstr, length );
    return 0;
}
```

执行结果：

``` cpp
str is 10 * 10 = 100, length is 13
```

### snprintf函数

&emsp;&emsp;函数原型如下：

``` cpp
int snprintf ( char* str, size_t size, const char* restrict format, ... );
```

`snprinf`比`sprintf`多了一个参数`size`，其作用是限制最大的写入数据量，可以避免`Buffer Overflow`：

``` cpp
#include <stdio.h>

int main() {
    char str[10];
    int ret = snprintf ( str, sizeof ( str ), "%s", "abcdefg" );
    printf ( "%d\n", ret );
    printf ( "%s\n", str );
    return 0;
}
```

执行结果：

``` cpp
7
abcdefg
```