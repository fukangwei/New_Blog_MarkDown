---
title: gethostbyaddr函数
categories: 网络编程
date: 2018-12-29 15:04:18
---
&emsp;&emsp;`gethostbyaddr`函数将`IP`地址转换为主机名：<!--more-->

``` cpp
#include <netdb.h>
struct hostent *gethostbyaddr ( const char *addr, size_t len, int family );
```

- `addr`是一个指向结构体`in_addr`或`in6_addr`的指针。
- `len`是此结构体的大小，对于`IPv4`地址为`4`，对于`IPv6`地址为`16`。
- `family`为`AF_INET`或`AF_INET6`。

``` cpp
#include <arpa/inet.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <netdb.h>

int main ( int argc, char **argv ) {
    struct in_addr addr;
    struct hostent *phost;

    if ( inet_pton ( AF_INET, argv[1], &addr ) <= 0 ) {
        printf ( "inet_pton error: %s\n", strerror ( errno ) );
        return -1;
    }

    phost = gethostbyaddr ( ( const char * ) &addr, sizeof ( addr ), AF_INET );

    if ( phost == NULL ) {
        printf ( "gethostbyaddr error: %s\n", strerror ( h_errno ) );
        return -1;
    }

    printf ( "host name: %s\n", phost->h_name );
    return 0;
}
```

执行结果：

``` bash
$ ./main 127.0.0.1
host name: localhost
```