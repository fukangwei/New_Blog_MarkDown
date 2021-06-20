---
title: vsnprintf和vprintf函数
categories: C语言语法详解
date: 2018-12-14 16:35:26
---
### vsnprintf函数

&emsp;&emsp;函数功能是将可变参数列表进行格式化，然后输出到一个字符数组中。<!--more-->

``` cpp
#include <stdarg.h>
int vsnprintf ( char* str, size_t size, const char* format, va_list ap );
```

- `str`：把格式化后的字符串存放在这里。
- `size`：`str`可接受的最大字符数。
- `format`：指定输出格式的字符串。
- `ap`：`va_list`型变量。

如果函数执行成功，则返回写入到字符数组`str`中的字符个数；如果函数执行失败，则返回`-1`。

``` cpp
#include <stdio.h>
#include <stdarg.h>

int mon_log ( char* format, ... ) {
    char str_tmp[50];
    int i = 0;
    va_list vArgList;
    va_start ( vArgList, format );
    i = vsnprintf ( str_tmp, 50, format, vArgList );
    printf ( "str_tmp is %s\n", str_tmp );
    va_end ( vArgList );
    return i;
}

int main() {
    char str[] = "asd";
    char format[] = "%s, %d, %d, %d";
    int i = mon_log ( format, str, 2, 3, 4 );
    printf ( "%d\n", i );
    return 0;
}
```

执行结果：

``` bash
str_tmp is asd, 2, 3, 4
12
```

### vprintf函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <stdio.h>
#include <stdarg.h>
int vprintf ( const char* format, va_list ap );
```

- `format`：指定输出格式的字符串。
- `ap`：`va_list`型变量。

如果函数执行成功，则返回实际输出的字符数；如果函数执行失败，则返回`-1`。

``` cpp
#include <stdio.h>
#include <stdarg.h>

int vpf ( char* fmt, ... ) {
    va_list argptr;
    int cnt;
    va_start ( argptr, fmt );
    cnt = vprintf ( fmt, argptr );
    va_end ( argptr );
    return cnt;
}

int main ( void ) {
    int inumber = 30;
    float fnumber = 90.0;
    char string[] = "abc";
    char fmt[] = "%d %f %s\n";
    vpf ( fmt, inumber, fnumber, string );
    return 0;
}
```

执行结果：

``` cpp
30 90.000000 abc
```