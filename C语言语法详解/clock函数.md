---
title: clock函数
categories: C语言语法详解
date: 2018-12-13 08:25:29
---
&emsp;&emsp;`clock`是`C/C++`中的计时函数，函数原型如下：<!--more-->

``` cpp
clock_t clock ( void );
```

该函数返回从`开启这个程序进程`到`程序中调用clock函数`之间的`clock tick`数。
&emsp;&emsp;`clock_t`是保存时间的数据类型，其定义在`time.h`中：

``` cpp
#ifndef _CLOCK_T_DEFINED
#define _CLOCK_T_DEFINED
typedef long clock_t;
#define CLOCKS_PER_SEC ( ( clock_t ) 1000 )
#endif
```

`time.h`还定义了一个常量`CLOCKS_PER_SEC`，它用来表示`1`秒中有多少个`clock tick`。
&emsp;&emsp;可以使用`clock`函数来计算某段代码的运行时间：

``` cpp
#include "stdio.h"
#include "stdlib.h"
#include "time.h"

int main ( void ) {
    long i = 10000000L;
    clock_t start, finish;
    double duration;
    printf ( "Time to do %ld empty loops is ", i );
    start = clock();

    while ( i-- ) ;

    finish = clock();
    duration = ( double ) ( finish - start ) / CLOCKS_PER_SEC;
    printf ( "%f seconds\n", duration );
    return 0;
}
```

执行结果：

``` cpp
Time to do 10000000 empty loops is 0.012710 seconds
```