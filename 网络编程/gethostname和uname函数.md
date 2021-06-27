---
title: gethostname和uname函数
categories: 网络编程
date: 2018-12-29 17:07:01
---
### gethostname

&emsp;&emsp;可以通过`gethostname`函数来获取主机名：<!--more-->

``` cpp
#include <unistd.h>
int gethostname ( char *name, size_t namelen );
```

该函数将主机名写入`name`指向的缓冲区中，该缓冲区的长度为`namelen`。如果执行成功，函数会返回`0`，否则返回`-1`。

### uname

&emsp;&emsp;可以使用`uname`得到关于主机的更为详细的信息：

``` cpp
#include <sys/utsname.h>
int uname ( struct utsname *name );
```

该函数将主机信息写入由`name`指向的结构体。`utsname`包含下列成员：

成员      | 描述         | 成员       | 描述
----------|-------------|------------|-----
`sysname` | 操作系统名   | `nodename` | 主机名
`release` | 系统发行级别 | `version`  | 系统版本号
`machine` | 硬件类型

如果函数执行成功，则返回一个非负数，否则返回`-1`。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <sys/utsname.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main() {
    char computer[256];
    struct utsname uts;

    if ( gethostname ( computer, 255 ) != 0 || uname ( &uts ) < 0 ) {
        fprintf ( stderr, "Could not get host information\n" );
        exit ( 1 );
    }

    printf ( "Computer host name is %s \n", computer );
    printf ( "System is %s on %s hardware \n", uts.sysname, uts.machine );
    printf ( "Nodename is %s\n", uts.nodename );
    printf ( "Version is %s, %s\n", uts.release, uts.version );
    return 0;
}
```

执行结果：

``` cpp
Computer host name is DESKTOP-AURQ86A
System is Linux on x86_64 hardware
Nodename is DESKTOP-AURQ86A
Version is 5.4.72-microsoft-standard-WSL2, #1 SMP Wed Oct 28 23:40:43 UTC 2020
```