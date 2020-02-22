---
title: clock函数
categories: C语言语法详解
date: 2018-12-13 08:25:29
---
&emsp;&emsp;`clock`是`C/C++`中的计时函数，而与其相关的数据类型是`clock_t`。在`MSDN`中，`clock`函数定义为：<!--more-->

``` cpp
clock_t clock ( void );
```

该函数返回从`开启这个程序进程`到`程序中调用clock函数`时之间的`CPU`时钟计时单元`clock tick`数，在`MSDN`中称之为挂钟时间`wal-clock`；若挂钟时间不可取，则返回`-1`。其中，`clock_t`是用来保存时间的数据类型。

### clock_t定义

&emsp;&emsp;在`time.h`文件中，我们可以找到它的定义：

``` cpp
#ifndef _CLOCK_T_DEFINED
typedef long clock_t;
#define _CLOCK_T_DEFINED
#endif
```

很明显，`clock_t`是一个长整形数。在`time.h`文件中，还定义了一个常量`CLOCKS_PER_SEC`，它用来表示一秒钟会有多少个时钟计时单元，其定义如下：

``` cpp
#define CLOCKS_PER_SEC ( ( clock_t ) 1000 )
```

在`Linux`系统下，`CLOCKS_PER_SEC`的值可能有所不同，目前`Linux`打印出来的值是`1000000`，表示的是微秒。
&emsp;&emsp;可以看到，每过千分之一秒(`1`毫秒)，调用`clock`函数返回的值就加`1`。你可以使用公式`clock()/CLOCKS_PER_SEC`来计算一个进程自身的运行时间：

``` cpp
void elapsed_time ( void ) {
    printf ( "Elapsed time:%u secs.\n", clock() / CLOCKS_PER_SEC );
}
```

当然，你也可以用`clock`函数来计算机器运行一个循环或者处理其它事件到底花了多少时间：

``` cpp
#include "stdio.h"
#include "stdlib.h"
#include "time.h"

int main ( void ) {
    long i = 10000000L;
    clock_t start, finish;
    double duration;
    /* 测量一个事件持续的时间 */
    printf ( "Time to do %ld empty loops is ", i );
    start = clock();

    while ( i-- ) ; /* 注意这行后面有个分号 */

    finish = clock();
    duration = ( double ) ( finish - start ) / CLOCKS_PER_SEC;
    printf ( "%f seconds\n", duration );
    system ( "pause" );
    return 0;
}
```

运行结果为`Time to do 10000000 empty loops is 0.03000 seconds`。从上面可以看到，时钟计时单元的长度为1毫秒，那么计时的精度也为1毫秒。我们可不可以通过改变`CLOCKS_PER_SEC`的定义，通过把它定义的大一些，从而使计时精度更高呢？通过尝试，你会发现这样是不行的。在标准`C/C++`中，最小的计时单位是1毫秒。

### 宏CLOCKS_PER_SEC

&emsp;&emsp;宏`CLOCKS_PER_SEC`适用于将计算系统时间类型转换为用户可读的秒时间，包含于头文件`ctime`(或`time.h`)中。在很久以前，人们就用`for`来进行延时，但是计算机处理速度越来越快，`for`循环已经无法实现精确延时，此时使用`clock`函数就可以实现这个功能：

``` cpp
#include <iostream>
#include <ctime> /* or time.h */

using namespace std;

int main() {
    int b = clock() / CLOCKS_PER_SEC;

    for ( int i = 0; i < 3; ) { /* 完成3秒定时 */
        i = clock() / CLOCKS_PER_SEC;
        i = i - b;
    }

    cout << "Finish it!" << endl;
    return 0;
}
```