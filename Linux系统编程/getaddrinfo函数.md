---
title: getaddrinfo函数
categories: Linux系统编程
date: 2019-02-02 21:03:58
---
### 概述

&emsp;&emsp;`IPv4`中使用`gethostbyname`函数完成主机名到地址解析，这个函数仅仅支持`IPv4`，且不允许调用者指定所需地址类型的任何信息，返回的结构只包含了用于存储`IPv4`地址的空间。`IPv6`中引入了`getaddrinfo`函数的新`API`，它是协议无关的，既可用于`IPv4`也可用于`IPv6`。`getaddrinfo`函数能够处理名字到地址以及服务到端口这两种转换，返回的是一个`addrinfo`的结构(列表)指针而不是一个地址清单，这些`addrinfo`结构随后可由套接口函数直接使用。如此以来，`getaddrinfo`函数把协议相关性安全隐藏在这个库函数内部。应用程序只要处理由`getaddrinfo`函数填写的套接口地址结构。该函数已经在`POSIX`规范中定义了。<!--more-->

### 函数说明

&emsp;&emsp;函数原型如下：

``` cpp
#include <netdb.h>
int getaddrinfo (
    const char *hostname, const char *service,
    const struct addrinfo *hints, struct addrinfo **result
);
```

- `hostname`：一个主机名或者地址串(`IPv4`的点分十进制串或者`IPv6`的`16`进制串)。
- `service`：服务名可以是十进制的端口号，也可以是已定义的服务名称，如`ftp`、`http`等。
- `hints`：可以是一个空指针，也可以是一个指向某个`addrinfo`结构体的指针，调用者在这个结构中填入关于期望返回的信息类型的暗示。举例来说，如果指定的服务既支持`TCP`也支持`UDP`，那么调用者可以把`hints`结构中的`ai_socktype`成员设置成`SOCK_DGRAM`，使得返回的仅仅是适用于数据报套接口的信息。
- `result`：本函数通过`result`指针参数返回一个指向`addrinfo`结构体链表的指针。

返回值为`0`表示成功，非`0`表示出错。

### 参数设置

&emsp;&emsp;在`getaddrinfo`函数之前通常需要对以下`6`个参数进行以下设置：`nodename`、`servname`、`hints`的`ai_flags`、`ai_family`、`ai_socktype`、`ai_protocol`。在`6`项参数中，对函数影响最大的是`nodename`，`sername`和`hints.ai_flag`，而`ai_family`只是有地址为`v4`地址或`v6`地址的区别。`ai_protocol`一般为`0`。
&emsp;&emsp;`getaddrinfo`在实际使用中的几种常用参数设置：一般情况下，`client/server`编程中，`server`端调用`bind`(如果面向连接的还需要`listen`)，`client`则不用`bind`函数，解析地址后直接`connect`(面向连接)或直接发送数据(无连接)。因此，比较常见的情况有：

- 通常服务器端在调用`getaddrinfo`之前，`ai_flags`设置`AI_PASSIVE`，用于`bind`；主机名`nodename`通常会设置为`NULL`，返回通配地址`[::]`。
- 客户端调用`getaddrinfo`时，`ai_flags`一般不设置`AI_PASSIVE`，但是主机名`nodename`和服务名`servname`(更愿意称之为`端口`)则应该不为空。
- 当然，即使不设置`AI_PASSIVE`，取出的地址也并非不可以被`bind`，很多程序中`ai_flags`直接设置为`0`，即`3`个标志位都不设置，这种情况下只要`hostname`和`servname`设置的没有问题就可以正确`bind`。

&emsp;&emsp;上述情况只是简单的`client/server`中的使用，但实际在使用`getaddrinfo`和参考国外开源代码的时候，曾遇到一些将`servname`(即端口)设为`NULL`的情况(当然，此时`nodename`必不为`NULL`，否则调用`getaddrinfo`会报错)。以下分情况进行了测试：

- 如果`nodename`是字符串型的`IPv6`地址，`bind`的时候会分配临时端口。
- 如果`nodename`是本机名，`servname`为`NULL`，则根据操作系统的不同略有不同。通常一台`IPv6`主机都有可能不止一个`IPv6`地址，比如`fe80::1`(本机`loopback`地址)、`fe80::***`的`Link-Local`地址、`3ffe::***`的全局地址等。这种情况下调用`getaddrinfo`会将这些地址全部返回，调用者应该注意如何使用这些地址。另外要注意的是，对于`fe80::`的地址在绑定的时候必须标明接口地址，也就是使用`fe80::20d:60ff:fe78:51c2%4`或`fe80::1%1`这样的地址格式。

### 使用细节

&emsp;&emsp;如果本函数返回成功，那么由`result`参数指向的变量已被填入一个指针，它指向的是由其中的`ai_next`成员串联起来的`addrinfo`结构链表。可以导致返回多个`addrinfo`结构的情形有以下`2`个：

- 如果与`hostname`参数关联的地址有多个，那么适用于所请求地址簇的每个地址都返回一个对应的结构。
- 如果`service`参数指定的服务支持多个套接口类型，那么每个套接口类型都可能返回一个对应的结构，具体取决于`hints`结构的`ai_socktype`成员。

&emsp;&emsp;我们必须先分配一个`hints`结构，把它清零后填写需要的字段，再调用`getaddrinfo`，然后遍历一个链表逐个尝试每个返回地址。
&emsp;&emsp;`getaddrinfo`解决了把主机名和服务名转换成套接口地址结构的问题。其中，如果`getaddrinfo`出错，那么返回一个非`0`的错误值。

``` cpp
#include <netdb.h>
const char *gai_strerror ( int error );
```

该函数以`getaddrinfo`返回的非`0`错误值的名字和含义为它的唯一参数，返回一个指向对应的出错信息串的指针。
&emsp;&emsp;由`getaddrinfo`返回的所有存储空间都是动态获取的，这些存储空间必须通过调用`freeaddrinfo`返回给系统。

``` cpp
#include < netdb.h >
void freeaddrinfo ( struct addrinfo *ai );
```

`ai`参数应指向由`getaddrinfo`返回的第一个`addrinfo`结构。这个链表中的所有结构以及它们指向的任何动态存储空间都被释放掉。
&emsp;&emsp;输入网址并输出`IP`地址的代码如下：

``` cpp
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <arpa/inet.h>

int main ( int argc, char **argv ) {
    if ( argc != 2 ) {
        printf ( "Usag...\n" );
        exit ( 1 );
    }

    struct addrinfo hints;
    struct addrinfo *res, *cur;
    int ret;
    struct sockaddr_in *addr;
    char ipbuf[16];

    memset ( &hints, 0, sizeof ( struct addrinfo ) );
    hints.ai_family = AF_INET; /* Allow IPv4 */
    hints.ai_flags = AI_PASSIVE; /* For wildcard IP address */
    hints.ai_protocol = 0; /* Any protocol */
    hints.ai_socktype = SOCK_STREAM;
    ret = getaddrinfo ( argv[1], NULL, &hints, &res );

    if ( ret == -1 ) {
        perror ( "getaddrinfo" );
        exit ( 1 );
    }

    for ( cur = res; cur != NULL; cur = cur->ai_next ) {
        addr = ( struct sockaddr_in * ) cur->ai_addr;
        printf ( "%s\n", inet_ntop ( AF_INET, &addr->sin_addr, ipbuf, 16 ) );
    }

    freeaddrinfo ( res );
    exit ( 0 );
}
```