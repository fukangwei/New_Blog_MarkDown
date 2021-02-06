---
title: Linux时间函数
categories: Linux系统编程
date: 2019-02-03 00:24:17
---
### Linux常用时间类型

#### time_t时间类型

&emsp;&emsp;`time_t`类型在`time.h`中定义：<!--more-->

``` cpp
#ifndef __TIME_T
#define __TIME_T

typedef long time_t;

#endif
```

可见，`time_t`实际是一个长整型。由于`time_t`类型长度的限制，它所表示的时间不能晚于`2038年1月19日03时14分07秒`。为了能够表示更久远的时间，可用`64`位或更长的整形数来保存日历时间。使用`time`函数获取当前时间的`time_t`值，使用`ctime`函数将`time_t`转为当地时间字符串。

#### struct tm时间类型

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
    int tm_wday; /* 星期，取值区间为0至6，0代表星期天，1代表星期1，以此类推  */
    int tm_yday; /* 从每年的1月1日开始的天数，取值区间为0至365，0代表1月1日 */
    /* 夏令时标识符，使用夏令时，tm_isdst为正；不使用夏令时，tm_isdst为0；不了解情况时，tm_isdst为负 */
    int tm_isdst;
};

#endif
```

&emsp;&emsp;`ANSI C`标准称使用`tm`结构的这种时间表示为分解时间(`broken-down time`)。使用`gmtime`和`localtime`可将`time_t`时间类型转换为`tm`结构体；使用`mktime`将`tm`结构体转换为`time_t`时间类型；使用`asctime`将`struct tm`转换为字符串形式。

#### struct timeval时间类型

&emsp;&emsp;`timeval`结构体在`time.h`中定义：

``` cpp
struct tmieval {
    time_t tv_sec;       /* 秒s    */
    suseconds_t tv_usec; /* 微秒us */
};
```

设置时间函数`settimeofday`与获取时间函数`gettimeofday`均使用该事件类型作为传参。

#### struct timespec时间类型

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

该函数返回从`1970`年`1`月`1`日`00`时`00`分`00`秒至今所经过的秒数。如果`time_t *timer`非空指针，函数也会将返回值存到`timer`指针指向的内存。函数执行成功则返回秒数，失败则返回`((time_t)-1)`，错误原因存于`errno`中。

``` cpp
time_t seconds;
seconds = time ( ( time_t * ) NULL );
```

#### ctime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
char *ctime ( const time_t *timep );
```

`ctime`将参数`timep`指向的`time_t`时间信息转换成实际所使用的时间日期表示方法，并以字符串形式返回。字符串格式为`Wed Jun 20 21:00:00 2012\n`。相当于`asctime(localtime(tp));`。

``` cpp
time_t timep;
tmep = time ( NULL );
printf ( "%s\n", ctime ( &timep ) );
```

#### gmtime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
struct tm *gmtime ( const time_t *timep );
```

`gmtime`将参数`timep`指向的`time_t`时间信息转换成以`tm`结构体表示的`GMT`时间信息，并以`struct tm*`指针返回。`GMT`是中央时区，北京在东`8`区，相差`8`个小时，所以`北京时间 = GMT时间 + 8小时`。

``` cpp
int main ( void ) {
    char *wday[] = {"Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"};
    time_t timep;
    struct tm *p_tm;
    timep = time ( NULL );
    p_tm = gmtime ( &timep ); /* 获取GMT时间 */
    printf ( "%d-%d-%d ", ( p_tm->tm_year + 1900 ), ( p_tm->mon + 1 ), p_tm->tm_mday );
    printf ( "%s %d:%d:%d\n", wday[p_tm->tm_wday], p_tm->tm_hour, p_tm->tm_min, p_tm->tm_sec );
}
```

#### localtime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
struct tm *localtime ( const time_t *timep );
```

`localtime`将参数`timep`指向的`time_t`时间信息转换成以`tm`结构体表示的本地时区时间(例如`北京时间 = GMT + 8小时`)。

``` cpp
int main ( void ) {
    char *wday[] = {"Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"};
    time_t timep;
    struct tm *p_tm;
    timep = time ( NULL );
    p_tm = localtime ( &timep ); /* 获取本地时区时间 */
    printf ( "%d-%d-%d ", ( p_tm->tm_year + 1900 ), ( p_tm->mon + 1 ), p_tm->tm_mday );
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

`mktime`将参数`p_tm`指向的`tm`结构体数据转换成从`1970`年`1`月`1`日`00`时`00`分`00`秒至今的`GMT`时间经过的秒数。

``` cpp
int main ( void ) {
    time_t timep;
    struct tm *p_tm;
    timep = time ( NULL );
    pintf ( "time( ):%d\n", timep );
    p_tm = local ( &timep );
    timep = mktime ( p_tm );
    printf ( "time()->localtime()->mktime():%d\n", timep );
    return 0;
}
```

#### asctime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
char *asctime ( const struct tm *p_tm );
```

`asctime`将参数`p_tm`指向的`tm`结构体数据转换成实际使用的时间日期表示方法，并以字符串形式返回(与`ctime`函数相同)。字符串格式为`Wed Jun 20 21:00:00 2012\n`。

``` cpp
int main ( void ) {
    time_t timep;
    timep = time ( NULL );
    printf ( "%s\n", asctime ( gmtime ( &timep ) ) );
    return 0;
}
```

#### difftime函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <time.h>
double difftime ( time_t timep1, time_t timep2 );
```

`difftime`比较参数`timep1`和`timep2`时间是否相同，并返回之间相差秒数。

``` cpp
int main ( void ) {
    time_t timep1, timep2;
    timep1 = time ( NULL );
    sleep ( 2 );
    timep2 = time ( NULL );
    printf ( "the difference is %f seconds\n", difftime ( timep1, timep2 ) );
    return 0;
}
```

#### gettimeofday函数

``` cpp
#include <sys/time.h>
#include <unistd.h>
int gettimeofday ( struct timeval *tv, struct timezone *tz );
```

`gettimeofday`把目前的时间信息存入`tv`指向的结构体，当地时区信息则放到`tz`指向的结构体。`struct timezone`原型如下：

``` cpp
struct timezone {
    int tz_minuteswest; /* miniutes west of Greenwich */
    int tz_dsttime; /* type of DST correction */
};
```

使用示例如下：

``` cpp
struct timeval tv;
struct timeval tz;

gettimeofday ( &tv, &tz );
```

使用`time`函数族获取时间并输出指定格式字符串例子(`strftime`函数)：

``` cpp
int main ( void ) {
    char strtime[20] = {0};
    time_t timep;
    struct tm *p_tm;
    timep = time ( NULL );
    p_tm = localtime ( &timep );
    strftime ( strtime, sizeof ( strtime ), "%Y-%m-%d %H:%M:%S", p_tm );
    return 0;
}
```

#### settimeofday函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <sys/time.h>
#include <unistd.h>
int settimeofday ( const struct timeval *tv, const struct timezone *gz );
```

`settimeofday`把当前时间设成由`tv`指向的结构体数据。当前地区信息则设成`tz`指向的结构体数据。

``` cpp
int main ( void ) {
    char t_string[] = "2012-04-28 22:30:00";
    struct tm time_tm;
    struct timeval time_tv;
    time_t timep;
    int ret = 0;
    sscanf ( t_string, "%d-%d-%d %d:%d:%d", &time_tm.tm_year,
             &time_tm.tm_mon, &time_tm.tm_mday, &time_tm.tm_hour,
             &time_tm.tm_min, &time_tm.tm_sec );
    time_tm.tm_year -= 1900;
    time_tm.tm_mon -= 1;
    time_tm.tm_wday = 0;
    time_tm.tm_yday = 0;
    time_tm.tm_isdst = 0;
    timep = mktime ( &time_tm );
    time_tv.tv_sec = timep;
    time_tv.tv_usec = 0;
    ret = settimeofday ( &time_tv, NULL );

    if ( ret != 0 ) {
        fprintf ( stderr, "settimeofday failed\n" );
        return -1;
    }

    return 0;
}
```

#### strftime函数

&emsp;&emsp;函数原型如下：

``` cpp
size_t strftime (
    char *str, size_t maxsize,
    const char *format, const struct tm *timeptr
);
```

该函数根据`format`中定义的格式化规则，格式化结构`timeptr`表示的时间，并把它存储在`str`中，最多为`maxsize`个字节。如果产生的字符串小于`maxsize`个字符(包括空结束字符)，则会返回复制到`str`中的字符总数(不包括空结束字符)，否则返回零。
&emsp;&emsp;`format`包含了普通字符和特殊格式说明符的任何组合，这些格式说明符由函数替换为表示`tm`中所指定时间的相对应值：

说明符 | 替换为                                                   | 实例
-------|---------------------------------------------------------|---------
`%a`   | 缩写的星期几名称                                          | `Sun`
`%A`   | 完整的星期几名称                                          | `Sunday`
`%b`   | 缩写的月份名称                                            | `Mar`
`%B`   | 完整的月份名称                                            | `March`
`%c`   | 日期和时间表示法                                          | `Sun Aug 19 02:56:02 2012`
`%d`   | 一月中的第几天(`01`至`31`)                                | `19`
`%H`   | `24`小时格式的小时(`00`至`23`)                            | `14`
`%I`   | `12`小时格式的小时(`01`至`12`)                            | `05`
`%j`   | 一年中的第几天(`001`至`366`)                              | `231`
`%m`   | 十进制数表示的月份(`01`至`12`)                             | `08`
`%M`   | 分(`00`至`59`)                                           | `55`
`%p`   | `AM`或`PM`                                               | `PM`
`%S`   | 秒(`00`至`61`)                                           | `02`
`%U`   | 一年中的第几周，以第一个星期日作为第一周的第一天(`00`至`53`) | `33`
`%w`   | 十进制数表示的星期几，星期日表示为`0`(`0`至`6`)             | `4`
`%W`   | 一年中的第几周，以第一个星期一作为第一周的第一天(`00`至`53`) | `34`
`%x`   | 日期表示法                                                | `08/19/12`
`%X`   | 时间表示法                                                | `02:50:06`
`%y`   | 年份，最后两个数字(`00`至`99`)                             | `01`
`%Y`   | 年份                                                      | `2012`
`%Z`   | 时区的名称或缩写                                           | `CDT`
`%%`   | 一个`%`符号                                               |

使用示例如下：

``` cpp
#include <stdio.h>
#include <time.h>

int main ( void ) {
    time_t rawtime;
    struct tm *info;
    char buffer[80];
    time ( &rawtime );
    info = localtime ( &rawtime );
    strftime ( buffer, 80, "%x - %I:%M%p", info );
    printf ( "格式化的日期 & 时间：|%s|\n", buffer );
    return ( 0 );
}
```

执行结果：

``` cpp
格式化的日期 & 时间：|08/23/12 - 12:40AM|
```


---

### 时间函数编程示例

&emsp;&emsp;示例`1`如下：

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

&emsp;&emsp;示例`2`如下：

``` cpp
#include <time.h>
#include <stdio.h>

int main ( void ) {
    struct tm *ptr;
    time_t lt;
    lt = time ( NULL ); /* 获取日历时间 */
    ptr = gmtime ( &lt ); /* 转化为格林威治时间 */
    printf ( asctime ( ptr ) ); /* 以格林威治时间的字符串方式打印 */
    printf ( ctime ( &lt ) ); /* 以本地时间的字符串方式打印 */
    return 0;
}
```

&emsp;&emsp;使用时间函数计算普通函数执行时间：

``` cpp
#include <sys/time.h>
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

void function() {
    unsigned int i, j;
    double y;

    for ( i = 0; i < 1000; i++ )
        for ( j = 0; j < 1000; j++ ) {
            y++;
        }
}

int main() {
    struct timeval tpstart, tpend;
    float timeuse;
    gettimeofday ( &tpstart, NULL ); /* 开始时间 */
    function();
    gettimeofday ( &tpend, NULL ); /* 结束时间 */
    /* 计算执行时间 */
    timeuse = 1000000 * ( tpend.tv_sec - tpstart.tv_sec ) + tpend.tv_usec - tpstart.tv_usec;
    timeuse /= 1000000;
    printf ( "Used Time:%f\n", timeuse );
    exit ( 0 );
}
```