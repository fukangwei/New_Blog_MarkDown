---
title: Linux系统编程基础
categories: Linux系统编程
date: 2019-03-19 13:43:45
---
#### 基于TCP的客户端

1. 创建一个`socket`，用函数`socket`。
2. 设置要连接的对方的`IP`地址和端口等属性。
3. 连接服务器，用函数`connect`。
4. 收发数据，用函数`send`和`recv`，或者`read`和`write`。
5. 关闭网络连接。

<img src="./Linux系统编程基础/3.png" height="244" width="277">

#### 基于UDP的服务器

1. 创建一个`socket`，用函数`socket`。
2. 绑定`IP`地址、端口等信息到`socket`上，用函数`bind`。
3. 设置对方的`IP`地址和端口等属性。
4. 发送数据，用函数`sendto`。
5. 关闭网络连接。

<img src="./Linux系统编程基础/4.png" height="296" width="374">

#### 服务器模型

&emsp;&emsp;在网络程序里面，一般来说都是许多客户对应一个服务器，为了处理客户的请求，对服务端的程序就提出了特殊的要求。目前最常用的服务器模型有：

- 循环服务器：服务器在同一个时刻只可以响应一个客户端的请求。
- 并发服务器：服务器在同一个时刻可以响应多个客户端的请求。

#### UDP循环服务器

&emsp;&emsp;`UDP`循环服务器的实现方法：`UDP`服务器每次从套接字上读取一个客户端的请求，然后进行处理，最后将结果返回给客户机。

``` cpp
socket ( ... );
bind ( ... );

while ( 1 ) {
    recvfrom ( ... );
    process ( ... );
    sendto ( ... );
}
```

因为`UDP`是非面向连接的，没有一个客户端可以老是占住服务端，服务器对于每一个客户机的请求总是能够满足。

#### TCP循环服务器

&emsp;&emsp;`TCP`服务器接受一个客户端的连接，然后处理，完成了这个客户的所有请求后，断开连接。算法如下：

``` cpp
socket ( ... );
bind ( ... );
listen ( ... );

while ( 1 ) {
    accept ( ... );
    process ( ... );
    close ( ... );
}
```

`TCP`循环服务器一次只能处理一个客户端的请求。只有在这个客户的所有请求都满足后，服务器才可以继续后面的请求。这样如果有一个客户端占住服务器不放时，其它的客户机都不能工作了，因此`TCP`服务器一般很少用循环服务器模型的。

#### TCP并发服务器

&emsp;&emsp;并发服务器的思想是每一个客户机的请求并不由服务器直接处理，而是由服务器创建一个子进程来处理。算法如下：

``` CPP
socket ( ... );
bind ( ... );
listen ( ... );

while ( 1 ) {
    accept ( ... );

    if ( fork ( ... ) == 0 ) {
        process ( ... );
        close ( ... );
        exit ( ... );
    }

    close ( ... );
}
```

`TCP`并发服务器可以解决`TCP`循环服务器客户机独占服务器的情况。但同时也带来了问题：为了响应客户的请求，服务器要创建子进程来处理，而创建子进程是一种非常消耗资源的操作。

#### 多路复用I/O

&emsp;&emsp;阻塞函数在完成其指定的任务以前不允许程序继续向下执行。例如，当服务器运行到`accept`语句时，而没有客户请求连接，服务器就会停止在`accept`语句上等待连接请求的到来，这种情况称为`阻塞`(`blocking`)。而非阻塞操作则可以立即完成，例如，如果你希望服务器仅仅检查是否有客户在等待连接，有就接受连接，否则就继续做其他事情，则可以通过使用`select`系统调用来实现。除此之外，`select`还可以同时监视多个套接字。

``` cpp
int select (
    int maxfd, fd_set *readfds, fd_set *writefds,
    fe_set *exceptfds, const struct timeval *timeout );
```

参数`Maxfd`是文件描述符的范围，比待检的最大文件描述符大`1`；`Readfds`是被读监控的文件描述符集；`Writefds`是被写监控的文件描述符集；`Exceptfds`是被异常监控的文件描述符集；`Timeout`是定时器，取不同的值，该调用有不同的表现：

- `Timeout`值为`0`，不管是否有文件满足要求，都立刻返回，无文件满足要求返回`0`，有文件满足要求返回一个正值。
- `Timeout`为`NULL`，`select`将阻塞进程，直到某个文件满足要求。
- `Timeout`值为正整数，就是等待的最长时间，即`select`在`timeout`时间内阻塞进程。

&emsp;&emsp;`Select`调用返回时，返回值有如下情况：

- 正常情况下返回满足要求的文件描述符个数。
- 经过了`timeout`等待后仍无文件满足要求，返回值为`0`。
- 如果`select`被某个信号中断，它将返回`-1`，并设置`errno`为`EINTR`。
- 如果出错，返回`-1`并设置相应的`errno`。

&emsp;&emsp;多路复用的步骤为：

1. 设置要监控的文件。
2. 调用`Select`开始监控。
3. 判断文件是否发生变化。

&emsp;&emsp;系统提供了`4`个宏对描述符集进行操作：

``` cpp
#include <sys/select.h>
void FD_SET ( int fd, fd_set *fdset );
void FD_CLR ( int fd, fd_set *fdset );
void FD_ZERO ( fd_set *fdset );
void FD_ISSET ( int fd, fd_set *fdset );
```

宏`FD_SET`将文件描述符`fd`添加到文件描述符集`fdset`中；宏`FD_CLR`从文件描述符集`fdset`中清除文件描述符`fd`；宏`FD_ZERO`清空文件描述符集`fdset`；在调用`select`后使用`FD_ISSET`来检测文件描述符集`fdset`中的文件`fd`发生了变化。
&emsp;&emsp;编程模型为：

``` cpp
FD_ZERO ( &fds ); /* 清空集合 */
sock1 = socket ( ... );
sock2 = socket ( ... );
bind ( sock1, ... );
bind ( sock2, ... );
listen ( sock1, ... );
listen ( sock1, ... );
FD_SET ( sock1, &fds ); /* 设置描述符 */
FD_SET ( sock2, &fds ); /* 设置描述符 */

maxfdp = ( sock1 > sock2 ? sock1 : sock2 ) + 1;

switch ( select ( maxfdp, &fds, NULL, NULL, &timeout ) ) {
    case -1: exit ( -1 ); break; /* select发送错误，退出程序 */
    case  0:              break;
    default:
        if ( FD_ISSET ( sock1, &fds ) ) { /* 测试sock1是否可读 */
            accpet ( sock1, ... );
        }

        break;
}
```