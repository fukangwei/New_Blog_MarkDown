---
title: printf输出
categories: C语言语法详解
date: 2018-12-13 15:32:18
---
### d格式

&emsp;&emsp;`d`格式用来输出十进制整数。有以下几种用法：<!--more-->

- `%d`：按整型数据的实际长度输出。
- `%ld`：输出长整型数据。
- `%md`：`m`为指定的输出字段的宽度。

1. 如果数据的位数小于`m`，则左端补以空格。
2. 如果数据的位数大于`m`，则按实际位数输出。

``` cpp
#include <stdio.h>

int main ( void ) {
    int a = 1000;
    long int b = 100000;
    printf ( "a = %d\n", a );
    printf ( "a = %6d\n", a );
    printf ( "a = %2d\n", a );
    printf ( "b = %ld\n", b );
    return 0;
}
```

执行结果：

``` cpp
a = 1000
a =   1000
a = 1000
b = 100000
```

### o格式

&emsp;&emsp;以无符号八进制形式输出整数。有以下几种用法：

- 对长整型可以用`%lo`格式输出。
- 可以用`%mo`格式指定输出字段宽度。

``` cpp
#include <stdio.h>

int main ( void ) {
    int a = 1000;
    long int b = 100000;
    printf ( "a = %o\n", a );
    printf ( "a = %6o\n", a );
    printf ( "a = %2o\n", a );
    printf ( "b = %lo\n", b );
    return 0;
}
```

执行结果：

``` cpp
a = 1750
a =   1750
a = 1750
b = 303240
```

### x格式或X格式

&emsp;&emsp;`x`格式或`X`格式以无符号十六进制形式输出整数：

- `x`格式让输出的字母为小写。
- `X`格式让输出的字母为大写。

有以下几种用法：

- 对长整型可以用`%lx`格式输出。
- 可以用`%mx`格式指定输出字段宽度。

``` cpp
#include <stdio.h>

int main ( void ) {
    int a = 1000;
    long int b = 100000;
    printf ( "a = %x\n", a );
    printf ( "a = %6x\n", a );
    printf ( "a = %2x\n", a );
    printf ( "b = %lx\n", b );
    return 0;
}
```

执行结果：

``` cpp
a = 3e8
a =    3e8
a = 3e8
b = 186a0
```

### u格式

&emsp;&emsp;`u`格式以无符号十进制形式输出整数。有以下几种用法：

- 对长整型可以用`%lu`格式输出。
- 可以用`%mu`格式输出指定字段宽度。

``` cpp
#include <stdio.h>

int main ( void ) {
    int a = 1000;
    long int b = 100000;
    printf ( "a = %u\n", a );
    printf ( "a = %6u\n", a );
    printf ( "a = %2u\n", a );
    printf ( "b = %lu\n", b );
    return 0;
}
```

执行结果：

``` cpp
a = 1000
a =   1000
a = 1000
b = 100000
```

### c格式

&emsp;&emsp;`c`格式用于输出一个字符。

``` cpp
#include <stdio.h>

int main ( void ) {
    int c = 'a';
    printf ( "a = %c\n", c ); /* 输出“a = a” */
    return 0;
}
```

### s格式

&emsp;&emsp;`s`格式用来输出一个字符串。有如下几种用法：

- `%s`：输出字符串。
- `%ms`：指定字符串输出的长度。

``` cpp
#include <stdio.h>

int main ( void ) {
    char str[] = "hi";
    printf ( "str = %s\n", str );
    printf ( "str = %6s\n", str );
    printf ( "str = %2s\n", str );
    return 0;
}
```

执行结果：

``` cpp
str = hi
str =     hi
str = hi
```

### f格式

&emsp;&emsp;`f`格式输出浮点数。有以下几种用法：

- `%f`：整数部分全部输出，并输出`6`位小数。
- `%m.nf`：输出共占`m`列，其中有`n`位小数。

``` cpp
#include <stdio.h>

int main ( void ) {
    float a = 1000.0;
    printf ( "a = %f\n", a );
    printf ( "a = %.2f\n", a );
    printf ( "a = %8.2f\n", a );
    return 0;
}
```

执行结果：

``` cpp
a = 1000.000000
a = 1000.00
a =  1000.00
```

### e格式或E格式

&emsp;&emsp;`e`格式或`E`格式以指数形式输出实数：

- `e`格式让输出的`e`为小写。
- `E`格式让输出的`E`为大写。

``` cpp
#include <stdio.h>

int main ( void ) {
    float a = 1000.0;
    printf ( "a = %e\n", a ); /* 输出“a = 1.000000e+03” */
    return 0;
}
```

### 0格式

&emsp;&emsp;`0`格式用于在输出数据前补零。

``` cpp
#include <stdio.h>

int main ( void ) {
    int a = 4;
    int b = 40000;
    printf ( "a = %03d\n", a );
    printf ( "b = %03d\n", b );
    return 0;
}
```

执行结果：

``` cpp
a = 004
b = 40000
```

### h格式

&emsp;&emsp;`h`格式输出`short`型数据。

``` cpp
#include <stdio.h>

int main ( void ) {
    short a = 2000;
    printf ( "a = %hd\n", a ); /* 输出“a = 2000” */
    return 0;
}
```