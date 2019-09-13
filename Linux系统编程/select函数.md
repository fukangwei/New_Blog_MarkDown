---
title: select函数
date: 2019-02-03 13:59:39
categories: Linux系统编程
---
&emsp;&emsp;`Select`在`Socket`编程中还是比较重要的，可是对于初学`Socket`的人来说都不太爱用`Select`写程序，他们只是习惯写诸如`connect`、`accept`、`recv`或`recvfrom`这样的阻塞程序。所谓阻塞方式`block`，顾名思义，就是进程或是线程执行到这些函数时必须等待某个事件的发生，如果事件没有发生，进程或线程就被阻塞，函数不能立即返回。可是使用`Select`就可以完成非阻塞(所谓非阻塞方式`non-block`，就是进程或线程执行此函数时不必非要等待事件的发生，一旦执行肯定返回，以返回值的不同来反映函数的执行情况，如果事件发生则与阻塞方式相同，若事件没有发生则返回一个代码来告知事件未发生，而进程或线程继续执行，所以效率较高)方式工作的程序，它能够监视我们需要监视的文件描述符的变化情况(读、写或是异常)。
&emsp;&emsp;`Select`函数原型如下：

``` cpp
int select (
    int maxfdp, fd_set *readfds, fd_set *writefds,
    fd_set *errorfds, struct timeval *timeout
);
```

&emsp;&emsp;先说明两个结构体：

- `struct fd_set`可以理解为一个集合，这个集合中存放的是文件描述符。这可以是我们所说的普通意义的文件，当然`Unix`下任何设备、管道、`FIFO`等都是文件形式，所以毫无疑问一个`socket`就是一个文件，`socket`句柄就是一个文件描述符。`fd_set`集合可以通过一些宏由人为来操作，比如清空集合为`FD_ZERO(fd_set *)`，将一个给定的文件描述符加入集合之中为`FD_SET(int, fd_set *)`，将一个给定的文件描述符从集合中删除为`FD_CLR(int, fd_set *)`，检查集合中指定的文件描述符是否可以读写为`FD_ISSET(int, fd_set *)`。
- `struct timeval`是一个常用的结构，用来代表时间值，有两个成员，一个是秒数，另一个是毫秒数。

&emsp;&emsp;具体解释`select`的参数：

- `int maxfdp`：它是一个整数值，是指集合中所有文件描述符的范围，即所有文件描述符的最大值加`1`。
- `fd_set *readfds`：它是指向`fd_set`结构的指针，这个集合中应该包括文件描述符，我们是要监视这些文件描述符的读变化的，即我们关心是否可以从这些文件中读取数据了。如果这个集合中有一个文件可读，`select`就会返回一个大于`0`的值，表示有文件可读；如果没有可读的文件，则根据`timeout`参数再判断是否超时，若超出`timeout`的时间，`select`返回`0`；若发生错误，则返回负值。可以传入`NULL`值，表示不关心任何文件的读变化。
- `fd_set *writefds`：它是指向`fd_set`结构的指针，这个集合中应该包括文件描述符，我们是要监视这些文件描述符的写变化的，即我们关心是否可以向这些文件中写入数据了。如果这个集合中有一个文件可写，`select`就会返回一个大于`0`的值，表示有文件可写；如果没有可写的文件，则根据`timeout`参数再判断是否超时，若超出`timeout`的时间，`select`返回`0`，若发生错误返回负值。可以传入`NULL`值，表示不关心任何文件的写变化。
- `fd_set *errorfds`：它和上面两个参数的意思一样，用来监视文件错误异常。
- `struct timeval* timeout`：它是`select`的超时时间，这个参数至关重要，它可以使`select`处于三种状态：第一，若将`NULL`以形参传入，即不传入时间结构，就是将`select`置于阻塞状态，一定等到监视文件描述符集合中某个文件描述符发生变化为止；第二，若将时间值设为`0`秒`0`毫秒，就变成一个纯粹的非阻塞函数，不管文件描述符是否有变化，都立刻返回，文件无变化返回`0`，有变化则返回一个正值；第三，`timeout`的值大于`0`，这就是等待的超时时间，即`select`在`timeout`时间内阻塞，超时时间之内有事件到来就返回了，否则在超时后不管怎样一定返回，返回值同上述。

&emsp;&emsp;返回值如下：

返回值 | 说明
-------|----
`负值` | `select`错误
`正值` | 某些文件可读写或出错
`0`    | 等待超时，没有可读写或错误的文件

&emsp;&emsp;在有了`select`后就可以写出像样的网络程序了。举个简单的例子，从网络上接收数据并写入一个文件中：

``` cpp
int main ( void ) {
    int sock;
    FILE *fp;
    struct fd_set fds;
    struct timeval timeout = {3, 0}; /* select等待3秒，要非阻塞就置0 */
    char buffer[256] = {0}; /* 256字节的接收缓冲区 */
    /* 假定已经建立UDP或TCP连接，主机ip和port都已经给定，要写的文件已经打开。*/
    sock = socket ( ... );
    bind ( ... );
    fp = fopen ( ... );
​
    while ( 1 ) {
        FD_ZERO ( &fds ); /* 每次循环都要清空集合，否则不能检测描述符变化 */
        FD_SET ( sock, &fds ); /* 添加描述符 */
        FD_SET ( fp, &fds ); /* 同上 */
        maxfdp = sock > fp ? sock + 1 : fp + 1; /* 描述符最大值加1 */
​
        switch ( select ( maxfdp, &fds, &fds, NULL, &timeout ) ) { /* select使用 */
            case -1: exit ( -1 ); /* select错误，退出程序 */ break;
            case  0: break; /* 再次轮询 */
            default:
                if ( FD_ISSET ( sock, &fds ) ) { /* 测试sock是否可读，即是否网络上有数据 */
                    recvfrom ( sock, buffer, 256, ... ); /* 接受网络数据 */
​
                    if ( FD_ISSET ( fp, &fds ) ) { /* 测试文件是否可写 */
                        fwrite ( fp, buffer, ... ); /* 写入文件 */
                    }
​
                    buffer清空;
                }
​
                break;
        }
    }
}
```

---

&emsp;&emsp;`select`函数原型如下：

``` cpp
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

int select (
    int n, fd_set *readfds, fd_set *writefds,
    fd_set *exceptfds, struct timeval *timeout
);
```

`select`用来等待文件描述词状态的改变。参数`n`代表最大的文件描述词加`1`，参数`readfds`、`writefds`和`exceptfds`称为描述词组，是用来回传该描述词的读、写或例外的状况。下面的宏提供了处理这三种描述词组的方式：

``` cpp
FD_CLR ( inr fd, fd_set *set );   /* 用来清除描述词组set中相关fd的位        */
FD_ISSET ( int fd, fd_set *set ); /* 用来测试描述词组set中相关fd的位是否为真 */
FD_SET ( int fd, fd_set *set );   /* 用来设置描述词组set中相关fd的位        */
FD_ZERO ( fd_set *set );          /* 用来清除描述词组set的全部位            */
```

参数`timeout`为结构`timeval`，用来设置`select`的等待时间：

``` cpp
struct timeval {
    time_t tv_sec;
    time_t tv_usec;
};
```

&emsp;&emsp;返回值：如果参数`timeout`设为`NULL`，则表示`select`没有`timeout`。
&emsp;&emsp;错误代码：执行成功则返回文件描述词状态已改变的个数，如果返回`0`代表在描述词状态改变前已超过`timeout`时间，当有错误发生时则返回`-1`，错误原因存于`errno`，此时参数`readfds`、`writefds`、`exceptfds`和`timeout`的值变成不可预测。

错误代码  | 说明
---------|-----
`EBADF`  | 文件描述词为无效的或该文件已关闭
`EINTR`  | 此调用被信号所中断
`EINVAL` | 参数`n`为负值
`ENOMEM` | 核心内存不足

&emsp;&emsp;常见的程序片段：

``` cpp
fs_set readset；
FD_ZERO ( &readset );
FD_SET ( fd, &readset );
select ( fd + 1, &readset, NULL, NULL, NULL );
​
if ( FD_ISSET ( fd, readset ) {
    /* ... */
}
```

&emsp;&emsp;下面是`linux`环境下`select`的简单用法：

``` cpp
#include <sys/time.h>
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <assert.h>
​
int main ( void ) {
    int keyboard;
    int ret, i;
    char c;
    fd_set readfd;
    struct timeval timeout;
    keyboard = open ( "/dev/tty", O_RDONLY | O_NONBLOCK );
    assert ( keyboard > 0 );
​
    while ( 1 ) {
        timeout.tv_sec = 1;
        timeout.tv_usec = 0;
        FD_ZERO ( &readfd );
        FD_SET ( keyboard, &readfd );
        ret = select ( keyboard + 1, &readfd, NULL, NULL, &timeout );
​
        if ( FD_ISSET ( keyboard, &readfd ) ) {
            i = read ( keyboard, &c, 1 );
​
            if ( '/n' == c ) {
                continue;
            }
​
            printf ( "hehethe input is %c\n", c );
​
            if ( 'q' == c ) {
                break;
            }
        }
    }
}
```

此代码用来循环读取键盘输入。将例子程序作一修改，加上了`timeout`，并且考虑了`select`返回的所有情况：

``` cpp
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <assert.h>
​
int main() {
    int keyboard;
    int ret, i;
    char c;
    fd_set readfd;
    struct timeval timeout;
    keyboard = open ( "/dev/tty", O_RDONLY | O_NONBLOCK );
    assert ( keyboard > 0 );
​
    while ( 1 ) {
        timeout.tv_sec = 5;
        timeout.tv_usec = 0;
        FD_ZERO ( &readfd );
        FD_SET ( keyboard, &readfd );
        ret = select ( keyboard + 1, &readfd, NULL, NULL, &timeout );
​
        if ( ret == -1 ) { /* select error when ret = -1 */
            perror ( "select error" );
        } else if ( ret ) { /* data coming when ret > 0 */
            if ( FD_ISSET ( keyboard, &readfd ) ) {
                i = read ( keyboard, &c, 1 );
​
                if ( '\n' == c ) {
                    continue;
                }
​
                printf ( "hehethe input is %c\n", c );
​
                if ( 'q' == c ) {
                    break;
                }
            }
        } else if ( ret == 0 ) { /* time out when ret = 0 */
            printf ( "time out\n" );
        }
    }
}
```