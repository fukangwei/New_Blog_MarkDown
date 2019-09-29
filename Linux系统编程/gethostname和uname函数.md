---
title: gethostname和uname函数
categories: Linux系统编程
abbrlink: b77dd00c
date: 2018-12-29 17:07:01
---
&emsp;&emsp;正如我们可以确定用户信息一样，程序也可以确定其运行的计算机的信息，`uname`命令提供了这些信息。`uname`同时也作为一个系统调用来在一个`C`程序中提供同样的信息，我们可以使用`man 2 uname`来查看详细的信息。
&emsp;&emsp;许多情况都需要主机信息。我们也许希望依据在网络中一个程序所运行的机器的名字来自定义其行为，也就是说，是一个学生的机器还是一个管理员的机器。为了授权的目的，我们也许希望限制一个程序只在一台机器上运行。所有这些都意味着我们需要一个方法来确定程序所运行的机器的信息。
&emsp;&emsp;如果系统安装了网络组件，那么我们可以非常容易地通过`gethostname`函数来得到其网络名：

``` c
#include <unistd.h>
int gethostname ( char *name, size_t namelen );
```

`gethostname`函数将机器的网络名写入`name`所指的字符串中。假定这个字符串的长度至少为`namelen`字符长。如果成功，`gethostname`函数会返回`0`，否则会返回`-1`。
&emsp;&emsp;我们可以由`uname`系统调用得到关于主机的更为详细的信息：

``` c
#include <sys/utsname.h>
int uname ( struct utsname *name );
```

`uname`函数将主机信息写入由`name`参数所指向的结构。`utsname`结构定义在`sys/ustname.h`中，大多数至少包含下列成员：

成员       | 描述
-----------|-----
`sysname`  | 操作系统名
`nodename` | 主机名
`release`  | 系统发行级别
`version`  | 系统版本号
`machine`  | 硬件类型

如果成功，`uname`函数会返回一个非负数，否则返回`-1`，同时设置`errno`来指示错误。
&emsp;&emsp;下面的程序用于得到主机信息：

``` c
#include <sys/utsname.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
​
int main() {
    char computer[256];
    struct utsname uts;
​
    if ( gethostname ( computer, 255 ) != 0 || uname ( &uts ) < 0 ) {
        fprintf ( stderr, "Could not get host information\n" );
        exit ( 1 );
    }
​
    printf ( "Computer host name is %s \n", computer );
    printf ( "System is %s on %s hardware \n", uts.sysname, uts.machine );
    printf ( "Nodename is %s\n", uts.nodename );
    printf ( "Version is %s, %s\n", uts.release, uts.version );
    exit ( 0 );
}
```

执行结果如下，如果我们的机器处在网络中，就会发现一个包含网络名在内的扩展主机名：

``` bash
Computer host name is fuxinzi-virtual-machine
System is Linux on x86_64 hardware
Nodename is fuxinzi-virtual-machine
Version is 4.4.0-53-generic,  # 74-Ubuntu SMP Fri Dec 2 15:59:10 UTC 2016
```

这个程序会调用`gethostname`函数来得到主机的网络名。注意，函数`uname`返回的字符串是依赖于具体实现的，在这个例子中，版本号包含内核编译的日期。
&emsp;&emsp;由`gethostid`函数可以得到一个唯一的主机号：

``` c
#include <unistd.h>
long gethostid ( void );
```

`gethostid`函数会返回一个唯一的主机号。授权管理通常使用这个函数来确保软件程序只可以运行一个具有合法授权的机器上。在`Sun`工作站上，它会返回一个机器在生产时设置在非易失性存储器中的一个数，所以对于系统硬件，这是唯一的。其他的系统(例如`Linux`)，会返回一个基于机器网络地址的值，但对于许可证管理来说还不够安全。