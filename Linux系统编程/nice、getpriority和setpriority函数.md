---
title: nice、getpriority和setpriority函数
categories: Linux系统编程
abbrlink: 41e52e29
date: 2019-02-03 17:11:07
---
&emsp;&emsp;可以通过改变进程的优先级来保证进程优先运行。在`Linux`下，通过系统调用`nice`可以改变进程的优先级。`nice`系统调用用来改变调用进程的优先级：

``` cpp
#include <unistd.h>
int nice ( int increment );
```

&emsp;&emsp;`getpriority`和`setpriority`函数原型如下：

``` cpp
#include <sys/resource.h>
int getpriority ( int which, int two );
int setpriority ( int which, int who, int prio );
```

`getpriority`函数返回一组进程的优先级，参数`which`和`who`组合确定返回哪一组进程的优先级。`which`的可能取值以及其意义如下：

- `PRIO_PROCESS`：一个特定的进程，此时`who`的取值为进程`ID`。
- `PRIO_PGRP`：一个进程组的所有进程，此时`who`的取值为进程组`ID`。
- `PRIO_USER`：一个用户拥有的所有进程，此时参数`who`取值为实际用户`ID`。

&emsp;&emsp;`getpriority`函数如果调用成功，返回指定进程的优先级，如果出错将返回`-1`，并设置`errno`的值。`errno`可能的取值如下：

- `ESRCH`：`which`和`who`的组合与现存的所有进程均不匹配。
- `EINVAL`：`which`是个无效的值。

&emsp;&emsp;注意，当指定的一组进程的优先级不同时，`getpriority`将返回其中优先级最低的一个。此外，当`getpriority`返回`-1`时，可能是发生错误，也有可能是返回的是指定进程的优先级。区分它们的惟一方法是在调用`getpriority`前将`errno`清零。如果函数返回`-1`且`errno`不为零，说明有错误产生。
&emsp;&emsp;`setpriority`函数用来指定进程的优先级，进程指定的方法与`getpriority`函数相同。如果调用成功，函数返回指定进程的优先级，出错则返回`-1`，并设置相应的`errno`。除了产生与`getpriority`相同的两个错误外，还有可能产生以下错误：

- `EPERM`：要设置优先级的进程与当前进程不属于同一个用户，并且当前进程没有`CAP_SYS_NICE`特许。
- `EACCES`：该调用可能降低进程的优先级并且进程没有`CAP_SYS_NICE`特许。

&emsp;&emsp;通过`getpriority`和`setpriority`完全可以改变进程的优先级，`nice`系统调用是它们的一种组合形式，`nice`系统调用等价于：

``` cpp
int nice ( int increamet ) {
    int oldpro = getpriority ( PRIO_PROCESS, getpid() );
    return setpriority ( PRIO_PROCESS, getpid(), oldpro + increament );
}
```

&emsp;&emsp;测试程序如下：

``` cpp
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/resource.h>
#include <sys/wait.h>
#include <stdlib.h>
​
int main ( void ) {
    pid_t pid;
    int stat_val = 0;
    int oldpri, newpri;
    printf ( "nice study\n" );
    pid = fork();
​
    switch ( pid ) {
        case 0:
            printf ( "Child is running, Curpid is %d, Parentpid is %d\n", pid, getppid() );
            oldpri = getpriority ( PRIO_PROCESS, getpid() );
            printf ( "Old priority = %d\n", oldpri );
            newpri = nice ( 2 );
            printf ( "New priority = %d\n", newpri );
            exit ( 0 );
        case -1: perror ( "Process creation failed\n" ); break;
        default:
            printf ( "Parent is running,Childpid is %d, Parentpid is %d\n", pid, getpid() );
            break;
    }
​
    wait ( &stat_val );
    exit ( 0 );
}
```

执行结果：

``` cpp
nice study
Parent is running,Childpid is 7068, Parentpid is 7067
Child is running, Curpid is 0, Parentpid is 7067
Old priority = 0
New priority = 2
```