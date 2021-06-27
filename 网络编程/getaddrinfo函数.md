---
title: getaddrinfo函数
categories: 网络编程
date: 2019-02-02 21:03:58
---
### getaddrinfo

&emsp;&emsp;一般使用`gethostbyname`完成主机名到地址解析，但它仅支持`IPv4`。而`getaddrinfo`函数是协议无关的，既可用于`IPv4`也可用于`IPv6`。<!--more-->

``` cpp
#include <netdb.h>
int getaddrinfo (
    const char *hostname, const char *service,
    const struct addrinfo *hints, struct addrinfo **result
);
```

- `hostname`：一个主机名，或者地址串，例如`192.168.1.1`。
- `service`：可以是十进制的端口号，也可以是已定义的服务名称，如`ftp`、`http`等。
- `hints`：可以是`NULL`，也可以是一个指向某个`addrinfo`结构体的指针。
- `result`：通过`result`返回一个指向`addrinfo`结构体链表的指针。

返回值为`0`表示成功，`非0`表示出错。

### freeaddrinfo

&emsp;&emsp;由`getaddrinfo`返回的所有存储空间都是动态获取的，这些存储空间必须通过`freeaddrinfo`返回给系统：

``` cpp
#include <netdb.h>
void freeaddrinfo ( struct addrinfo *ai );
```

### 代码实例

&emsp;&emsp;输入网址并输出`IP`地址：

``` cpp
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <arpa/inet.h>

int main ( int argc, char **argv ) {
    int ret;
    char ipbuf[16];
    struct addrinfo hints;
    struct addrinfo *res, *cur;
    struct sockaddr_in *addr;
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