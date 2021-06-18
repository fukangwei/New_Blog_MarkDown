---
title: goto语句
categories: C语言语法详解
date: 2018-12-06 12:42:02
---
&emsp;&emsp;`goto`语句是一种无条件转移语句：<!--more-->

``` cpp
goto 标号;
```

执行`goto`语句后，程序将跳转到该`标号`处，并执行其后的语句。
&emsp;&emsp;注意，标号必须与`goto`语句同处于一个函数中，`goto`不能跳到函数体外的函数。

``` cpp
#include <stdio.h>

int main ( void ) {
    int i, sum = 0;
    i = 1;
loop:

    if ( i <= 100 ) {
        sum = sum + i;
        i++;
        goto loop;
    }

    printf ( "%d\n", sum );
    return 0;
}
```

&emsp;&emsp;`goto`语句最常见的用法如下：

``` cpp
if (do_something() == ERR)
    goto error;
if (do_something2() == ERR)
    goto error;
if (do_something3() == ERR)
    goto error;
if (do_something4() == ERR)
    goto error;

error:
    exit();
```

&emsp;&emsp;`goto`语句不经常使用，主要是因为它会使程序的层次不清晰。