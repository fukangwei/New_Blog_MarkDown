---
title: Linux时间函数
categories: Linux系统编程
date: 2019-02-03 00:24:17
---
### Linux常用时间类型

#### time_t

&emsp;&emsp;`time_t`类型在`time.h`中定义：<!--more-->

``` cpp
#ifndef __TIME_T
#define __TIME_T

typedef long time_t;

#endif
```

#### struct tm

&emsp;&emsp;`tm`结构在`time.h`中定义：

``` cpp
#ifndef _TM_DEFINED
#define _TM_DEFINED

struct tm {
    int tm_sec;  /* 秒，取值区间为0至59                                */
    int tm_min;  /* 分，取值区间为0至59                                */
    int tm_hour; /* 时，取值区间为0至23                                */
    int tm_mday; /* 日，取值区间为1至31                                */
    int tm_mon;  /* 月份，取值区间为0至11                              */
    int tm_year; /* 年份，其值为1900年至今年数                          */
    int tm_wday; /* 星期，取值区间为0至6，0代表星期天，1代表星期1，以此类推 */
    int tm_yday; /* 从每年的1月1日开始的天数，取值区间为0至365，0代表1月1日 */
    /* 夏令时标识符：
       1. 使用夏令时，tm_isdst为正。
       2. 不使用夏令时，tm_isdst为0。
       3. 不了解情况时，tm_isdst为负 */
    int tm_isdst;
};

#endif
```

#### struct timeval

&emsp;&emsp;`timeval`结构体在`time.h`中定义：

``` cpp
struct tmieval {
    time_t tv_sec;       /* 秒s    */
    suseconds_t tv_usec; /* 微秒us */
};
```

#### struct timespec

&emsp;&emsp;`timespec`结构体在`time.h`中定义：

``` cpp
struct timespec {
    time_t tv_sec; /* 秒s    */
    long tv_nsec;  /* 纳秒ns */
};
```

### Linux下常用时间函数

#### time函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
time_t time ( time_t *timer );
```

函数返回从`1970`年`1`月`1`日`00`时`00`分`00`秒至今所经过的秒数。
&emsp;&emsp;如果`time_t *timer`为非空指针，函数也会将返回值存到`timer`指向的内存。

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    time_t seconds;
    seconds = time ( ( time_t * ) NULL );
    printf ( "seconds is %ld\n", seconds );
}
```

#### ctime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
char *ctime ( const time_t *timep );
```

函数将参数`timep`指向的`time_t`时间信息转换成实际所使用的时间日期表示方法，并以字符串形式返回。

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    time_t seconds;
    seconds = time ( NULL );
    printf ( "%s", ctime ( &seconds ) );
}
```

#### gmtime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
struct tm *gmtime ( const time_t *timep );
```

函数将参数`timep`指向的`time_t`时间信息转换成以`tm`结构体表示的`GMT`时间信息，并以`struct tm *`指针返回。
&emsp;&emsp;`GMT`是中央时区，北京在东`8`区，相差`8`个小时，所以`北京时间 = GMT时间 + 8小时`。

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    struct tm *local;
    time_t t;
    t = time ( NULL ); /* 获取日历时间 */
    local = localtime ( &t ); /* 将日历时间转化为本地时间 */
    printf ( "Local hour is: %d\n", local->tm_hour ); /* 打印当前的小时值 */
    local = gmtime ( &t ); /* 将日历时间转化为格林威治时间 */
    printf ( "UTC hour is: %d\n", local->tm_hour );
    return 0;
}
```

#### localtime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
struct tm *localtime ( const time_t *timep );
```

该函数将参数`timep`指向的`time_t`时间信息转换成以`tm`结构体表示的本地时区时间。

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    char *wday[] = {
        ( char* ) "Sun", ( char* ) "Mon", ( char* ) "Tue", ( char* ) "Wed",
        ( char* ) "Thu", ( char* ) "Fri", ( char* ) "Sat"
    };
    time_t timep;
    struct tm *p_tm;
    timep = time ( NULL );
    p_tm = localtime ( &timep ); /* 获取本地时区时间 */
    printf ( "%d-%d-%d ", ( p_tm->tm_year + 1900 ), ( p_tm->tm_mon + 1 ), p_tm->tm_mday );
    printf ( "%s %d:%d:%d\n", wday[p_tm->tm_wday], p_tm->tm_hour, p_tm->tm_min, p_tm->tm_sec );
    return 0;
}
```

#### mktime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
time_t mktime ( struct tm *p_tm );
```

该函数将参数`p_tm`指向的`tm`结构体数据转换成从`1970`年`1`月`1`日`00`时`00`分`00`秒至今的`GMT`时间经过的秒数。

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    time_t timep;
    struct tm *p_tm;
    timep = time ( NULL );
    printf ( "time(): %ld\n", timep );
    p_tm = localtime ( &timep );
    timep = mktime ( p_tm );
    printf ( "time() -> localtime() -> mktime(): %ld\n", timep );
    return 0;
}
```

#### asctime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
char *asctime ( const struct tm *p_tm );
```

该函数将参数`p_tm`指向的`tm`结构体数据转换成实际使用的时间日期表示方法，并以字符串形式返回。

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    time_t timep;
    timep = time ( NULL );
    printf ( "%s", asctime ( gmtime ( &timep ) ) );
    return 0;
}
```

#### difftime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
double difftime ( time_t timep1, time_t timep2 );
```

该函数比较参数`timep1`和`timep2`时间是否相同，并返回之间相差秒数。

``` cpp
#include <time.h>
#include <stdio.h>
#include <unistd.h>

int main ( void ) {
    time_t timep1, timep2;
    timep1 = time ( NULL );
    sleep ( 2 );
    timep2 = time ( NULL );
    printf ( "the difference is %f seconds\n", difftime ( timep1, timep2 ) );
    return 0;
}
```