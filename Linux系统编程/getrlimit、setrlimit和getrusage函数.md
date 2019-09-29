---
title: getrlimit、setrlimit和getrusage函数
categories: Linux系统编程
abbrlink: c94db408
date: 2019-02-03 17:50:03
---
&emsp;&emsp;为了支持多用户同时登录以及多个应用连接，`UNIX`系统给系统管理员提供了控制系统资源的许多方法。这种资源限制包括`CPU`时间、内存使用量以及磁盘使用量。资源控制允许你调整系统到最佳的使用率。`UNIX`的早期版本中，一些在编译时设置的系统限制如果需要修改，则需要重新编译整个系统。然而，如果并非所有的运行中的系统资源都需要重新编译整个系统，那么现代的`UNIX`系统可以调整大多数这些资源的限制。
&emsp;&emsp;`getrlimit`允许一个进程查询所受的的系统限制，这些系统限制通过一对硬/软限制对来指定。当一个软限制被超过时，进程还可以继续，当然这取决于限制的类型，同时一个信号会发送给进程。另一方面，进程不可以超过它的硬限制。软限制值可以被进程设置在位于`0`和最大硬限制间的任意值。硬限制值不能被任何进程降低，仅仅超级用户可以增加之。

``` cpp
#include <sys/types.h>
#include <sys/time.h>
#include <sys/resource.h>
int getrlimit ( int resource, struct rlimit *rlp );
int setrlimit ( int resource, const struct rlimit *rlp );
```

`getrlimit`和`setrlimit`都使用下面的数据结构：

``` cpp
struct rlimit {
    rlim_t rlim_cur;
    rlim_t rlim_max;
};
```

`rlim_cur`为指定的资源指定当前的系统软限制，`rlim_max`将为指定的资源指定当前的系统硬限制。
&emsp;&emsp;`getrlimit`和`setrlimit`函数的第一个参数是资源参数，这个参数用来指定进程获取信息的那个资源。可能的资源值如下所示，你也可以在`/usr/include/sys/resource.h`中找到它们：

``` cpp
#define RLIMIT_CPU     0  /* cpu time in milliseconds                 */
#define RLIMIT_FSIZE   1  /* maximum file size                        */
#define RLIMIT_DATA    2  /* data size                                */
#define RLIMIT_STACK   3  /* stack size                               */
#define RLIMIT_CORE    4  /* core file size                           */
#define RLIMIT_RSS     5  /* resident set size                        */
#define RLIMIT_MEMLOCK 6  /* locked-in-memory address space           */
#define RLIMIT_NPROC   7  /* number of processes                      */
#define RLIMIT_NOFILE  8  /* number of open files                     */
#define RLIMIT_SBSIZE  9  /* maximum size of all socket buffers       */
#define RLIMIT_VMEM    10 /* virtual process size (inclusive of mmap) */
```

- `RLIMIT_CPU`资源限制指定一个进程可以取得`CPU`执行任务的毫秒数。一般地，一个进程仅仅有一个软限制而没有硬限制。如果超出软限制，进程会收到一个`SIGXCPU`信号。
- `RLIMIT_FSIZE`限制指定一个进程可以创建的最大文件大小，以字节为单位。比如，如果`RLIMIT_FSIZE`设置为`0`，那么进程将根本不能创建文件。如果进程超出此限制，就会发出`SIGFSZ`信号。
- `RLIMIT_DATA`限制指定一个进程数据段可占据的最大字节值。一个进程的数据段就是放置动态内存的一个区域(`C/C++`用`malloc`分配的内存)。如果超出限制，分配新内存的操作将会遭到失败。
- `RLIMIT_STACK`限制指定进程栈可占据的最大字节数。一旦超出硬限制，进程会收到`SIGSEV`信号。
- `RLIMIT_CORE`限制指定了进程可以创建的最大`core`文件的大小。如果此限制设为`0`，将不能创建。另外，当达到此限制时，所有正在写`core`文件的进程都将被中断。
- `RMIMIT_RSS`限制了进程的常驻集大小(`resident set size`)可占据的最大字节数，这个进程的常驻集和进程所使用的物理内存数有关。
- `RLIMIT_MEMLOCK`限制指定了进程可以使用系统调用到`mlock`进行锁定的最大字节数。
- `RLIMIT_NPROC`限制指定了一个指定用户可以开启的最多并发进程数，这里的用户是通过进程来确定的有效用户`ID`。
- `RLIMIT_NOFILE`限制指定了进程可以打开的最多文件数。
- `RLIMIT_SBSIZE`限制指定用户在任何时刻可使用的`mbufs`数。
- `RLIMIT_VMEM`限制说明一个进程的映射地址空间可以占据的字节数。如果超出限制，分配动态内存和到`mmap`的调用会失败。

&emsp;&emsp;`RLIM_INFINITY`宏用来去除对一个资源的限制。换句话说，将一个资源的硬限制设置为`RLIM_INFINITY`，将会导致此种资源的使用没有任何系统限制；将软限制设置为`RLIM_INFINITY`将会阻止进程收到任何软限制警告。如果你的进程不想为那些会导致进程在超过软限制时发送信号的资源设置一个信号处理器，这个参数将变得非常有用。
&emsp;&emsp;如果使用了`getrlimit`参数，那么第二个参数需要设置为一个到`rlimit`结构的有效指针。然后`getrlimit`会将适当的限制值放入此结构。另外，在改变限制时，`setrlimit`会使用在第二个参数中设置值。将值设置为`0`将会阻止使用此资源。将值设置为`RLIM_INFINITY`会除去对该资源的所有限制。这些函数都在执行成功后都返回`0`，反之为`-1`。有任何错误产生，这些函数会相应的设置`errno`。
&emsp;&emsp;`getpagesize`函数原型如下：

``` cpp
#include <unistd.h>
int getpagesize ( void );
```

在介绍`getrusage`函数前，我们需要讨论一下`getpagesize`函数。一些进程状态是根据使用的分页(`pages`)来显示的。分页的内存仅仅是一段内存，通常为`4096`字节左右。但是分页大小却千差万别，并且它不会固定编入(`hard-coded`)你的系统。取而代之的是，要确定本地系统的分页大小(`pagesize`)需要使用`getpagesize`函数。`getpagesize`的返回值就是每个分页使用的字节数。
&emsp;&emsp;现在我们知道如何查看系统限制，还需要知道如何确定当前进程资源的使用量，`getrusage`函数就是用于此目的。一个进程可以确定它的内存使用量、`CPU`执行时间、甚至可获得其子进程的相关信息。因此，`getrusage`函数的一个用途就是帮助系统避免逃逸进程(`runaway`)的出现。逃逸进程指的是那些不受系统控制的进程，它们或者使用了过多的`CPU`(比如循环调用)、或者是超过了内存使用量的限制(导致内存泄漏)。

``` cpp
#include <sys/types.h>
#include <sys/time.h>
#include <sys/resource.h>
​
#define RUSAGE_SELF      0
#define RUSAGE_CHILDREN -1
​
int getrusage ( int who, struct rusage *rusage );
```

参数`who`可以设置为`RUSAGE_SELF`或者`RUSAGE_CHILDREN`。如果设置成`RUSAGE_SELF`，那么将会以当前进程的相关信息来填充`rusage`结构。反之，如果设置成`RUSAGE_CHILDREN`，那么`rusage`结构中的数据都将是当前进程的子进程的信息。
&emsp;&emsp;`rusage`结构体定义在`/usr/include/sys/resource.h`中，它含有以下成员变量：

``` cpp
struct rusage {
    struct timeval ru_utime;
    struct timeval ru_stime;
    long ru_maxrss;
    long ru_ixrss;
    long ru_idrss;
    long ru_isrss;
    long ru_minflt;
    long ru_majflt;
    long ru_nswap;
    long ru_inblock;
    long ru_oublock;
    long ru_msgsnd;
    long ru_msgrcv;
    long ru_nsignals;
    long ru_nvcsw;
    long ru_nivcsw;
};
```

- `ru_utime`和`ru_stime`：它们包含了在用户模式和系统模式中执行时间的总和，都使用`timeval`结构。
- `ru_maxrss`：保存了常驻集的内存使用数，其值根据内存分页的使用来确定。
- `ru_ixrss`：指文本段(`text segment`)使用的内存数乘以执行滴答数。
- `ru_idrss`：指进程所使用的私有内存数(`KB`)乘以执行滴答数。
- `ru_isrss`：指栈使用的内存数(`KB`为单位)乘以执行滴答数。
- `ru_minflt`：指不需要`I/O`的页缺失数，页缺失发生在内核需要得到一个内存页以供进程访问时。
- `ru_majflt`：指需要`I/O`的页缺失数，页缺失发生在内核需要得到一个内存页以供进程访问时。
- `ru_nswap`：有时一个进程会被调出内存，以提供空间给其他进程使用。`ru_nswap`指的就是一个进程将要调出内存的次数。
- `ru_inblock`：指文件系统需要为一个读请求执行输入操作的次数。
- `ru_oublock`：指文件系统需要为一个写入请求执行输出操作的次数。
- `ru_msgsnd`：指发送的`IPC`信息总数
- `ru_msgrcv`：指收到的`IPC`信息总数。
- `ru_nsignals`：指进程收到的信号总数。
- `ru_nvcsw`：一个进程主动上下文切换总数。主动上下文切换发生在一个进程放弃它的`CPU`分时时，通常是一个进程等待某可用资源时。
- `ru_nivcsw`：包含了因高优先级进程运行导致的上下文切换总数。

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/resource.h>
​
int main ( void ) {
    struct rusage start;
    struct rusage end;
    FILE *fp;
    int rc, i;
    double usertime, kerneltime;
    rc = getrusage ( RUSAGE_SELF, &start );
    fp = NULL;
    fp = fopen ( "getrusage.log", "a+" );
​
    if ( fp != NULL ) {
        for ( i = 0; i < 10000000; i++ ) {
            fprintf ( fp, "%d", i );
        }
    }
​
    fclose ( fp );
    rc = getrusage ( RUSAGE_SELF, &end );
    usertime = ( end.ru_utime.tv_sec - start.ru_utime.tv_sec ) + \
               ( end.ru_utime.tv_usec - start.ru_utime.tv_usec ) / 10e6;
    kerneltime = ( end.ru_stime.tv_sec - start.ru_stime.tv_sec ) + \
                 ( end.ru_stime.tv_usec - start.ru_stime.tv_usec ) / 10e6;
    printf ( "The user time is: %f\n", usertime );
    printf ( "The system_time is: %f\n", kerneltime );
    return 0;
}
```