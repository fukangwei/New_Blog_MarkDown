---
title: errno和strerror
categories: Linux系统编程
date: 2019-02-03 13:52:53
---
### errno

&emsp;&emsp;在调用`Linux`系统`API`的时候，可能出现一些错误，例如使用`open`有些时候会返回`-1`。这个时候需要知道失败的原因，于是全局变量`errno`就相当有用了。<!--more-->

``` cpp
#include <stdio.h>
#include <errno.h>
#include <fcntl.h>

int main ( void ) {
    int fd;
    extern int errno;

    if ( ( fd = open ( "/dev/dsp", O_WRONLY ) ) < 0 ) {
        printf ( "errno = %d\n", errno );
        return 1;
    }

    return 0;
}
```

如果`dsp`设备忙的话，`errno`的值将是`16`。

### strerror

&emsp;&emsp;可以使用`strerror`来翻译`error`：

``` cpp
#include <stdio.h>
#include <errno.h>
#include <fcntl.h>
#include <string.h>

int main ( void ) {
    int fd;
    extern int errno;

    if ( ( fd = open ( "/dev/dsp", O_WRONLY ) ) < 0 ) {
        printf ( "errno = %d\n", errno );
        char *mesg = strerror ( errno );
        printf ( "Mesg: %s\n", mesg );
    }

    return 0;
}
```

`dsp`设备忙的话，将输出如下信息：

``` cpp
errno = 16
Mesg: Device or resource busy
```