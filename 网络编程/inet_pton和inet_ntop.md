---
title: inet_pton和inet_ntop
abbrlink: 997a84b0
date: 2019-01-17 09:12:00
categories: 网络编程
---
&emsp;&emsp;`Linux`下这`2`个`IP`地址转换函数，可以将`IP`地址在`点分十进制`和`整数`之间进行转换。而且，`inet_pton`和`inet_ntop`能够处理`IPv4`地址和`IPv6`地址。
&emsp;&emsp;`inet_pton`函数原型如下(将`点分十进制`转为`整数`)：

``` cpp
#include <sys/types.h>
#include <sys/socket.h>
#include <arpa/inet.h>

int inet_pton ( int af, const char *src, void *dst );
```

这个函数转换字符串到网络地址，参数`af`是地址族，转换后存在`dst`中。`inet_pton`是`inet_addr`的扩展，支持的多地址族如下：

- `af = AF_INET`：`src`为指向字符型的地址，即`ASCII`的地址的首地址(`ddd.ddd.ddd.ddd`格式)，函数将该地址转换为`in_addr`的结构体，并复制在`*dst`中。
- `af = AF_INET6`：`src`为指向`IPv6`的地址，函数将该地址转换为`in6_addr`的结构体，并复制在`*dst`中。

如果函数出错将返回一个负值，并将`errno`设置为`EAFNOSUPPORT`。如果参数`af`指定的地址族和`src`格式不对，函数将返回`0`。
&emsp;&emsp;`inet_ntop`函数原型如下(将`整数`转为`点分十进制`)：

``` cpp
#include <sys/types.h>
#include <sys/socket.h>
#include <arpa/inet.h>

const char *inet_ntop ( int af, const void *src, char *dst, socklen_t cnt );
```

这个函数转换网络二进制结构到`ASCII`类型的地址，参数的作用和`inet_pton`相同，只是多了一个参数`socklen_t cnt`，它是所指向缓存区`dst`的大小，作用是避免溢出。如果缓存区太小无法存储地址的值，则返回一个空指针，并将`errno`置为`ENOSPC`。

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
​
int main ( void ) {
    char IPdotdec[20]; /* 存放点分十进制IP地址 */
    struct in_addr s; /* IPv4地址结构体 */
    printf ( "Please input IP address: " );
    scanf ( "%s", IPdotdec );
    inet_pton ( AF_INET, IPdotdec, ( void * ) &s ); /* 转换 */
    printf ( "inet_pton: 0x%x\n", s.s_addr ); /* 注意得到的字节序 */
    inet_ntop ( AF_INET, ( void * ) &s, IPdotdec, 16 ); /* 反转换 */
    printf ( "inet_ntop: %s\n", IPdotdec );
}
```