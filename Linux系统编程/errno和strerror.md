---
title: errno和strerror
categories: Linux系统编程
abbrlink: 501b0301
date: 2019-02-03 13:52:53
---
&emsp;&emsp;经常在调用`Linux`系统`API`的时候出现一些错误，比如使用`open`、`write`、`creat`之类的函数有些时候会返回`-1`，也就是调用失败。这个时候往往需要知道失败的原因，使用`errno`这个全局变量就相当有用了。
&emsp;&emsp;在程序代码中包含头文件`errno.h`，然后每次程序调用失败的时候，系统会自动用用错误代码填充`errno`这个全局变量，这样你只需要读`errno`这个全局变量就可以获得失败原因了。

``` cpp
#include <stdio.h>
#include <string.h>
#include <errno.h>
​
int main ( void ) {
    int fd;
    extern int errno;
​
    if ( ( fd = open ( "/dev/dsp", O_WRONLY ) ) < 0 ) {
        printf ( "errno = %d\n", errno );
    }
​
    exit ( 0 );
}
```

如果`dsp`设备忙的话，`errno`值将是`16`。
&emsp;&emsp;查看错误代码`errno`是调试程序的一个重要方法，当`Linux`的`C`语言`API`函数发生异常时，一般会将`errno`变量赋一个整数值，不同的值表示不同的含义，可以通过查看该值推测出错的原因。比较麻烦的是每次都要去`Linux`源代码里面查找错误代码的含义，以下来自`linux 2.4.20-18`内核代码中的`/usr/include/asm/errno.h`文件：

``` cpp
#ifndef _I386_ERRNO_H
#define _I386_ERRNO_H
​
#define EPERM   1  /* Operation not permitted   */
#define ENOENT  2  /* No such file or directory */
#define ESRCH   3  /* No such process           */
#define EINTR   4  /* Interrupted system call   */
#define EIO     5  /* I/O error                 */
#define ENXIO   6  /* No such device or address */
#define E2BIG   7  /* Arg list too long         */
#define ENOEXEC 8  /* Exec format error         */
#define EBADF   9  /* Bad file number           */
#define ECHILD  10 /* No child processes        */
...​
#endif
```

也可以使用`strerror`来翻译：

``` cpp
#include <stdio.h>
#include <string.h>
#include <errno.h>
​
int main ( void ) {
    int fd;
    extern int errno;
​
    if ( ( fd = open ( "/dev/dsp", O_WRONLY ) ) < 0 ) {
        printf ( "errno = %d\n", errno );
        char *mesg = strerror ( errno );
        printf ( "Mesg: %s\n", mesg );
    }
​
    exit ( 0 );
}
```

`dsp`设备忙的话将输出如下信息：

``` cpp
errno = 16
Mesg: Device or resource busy
```