---
title: gethostbyaddr函数
date: 2018-12-29 15:04:18
tags:
---
&emsp;&emsp;函数`gethostbyaddr`取一个二进制的`IP`地址并试图找到对应于此地址的主机名，与`gethostbyname`的行为恰好相反。其原型如下所示：

``` c
#include <netdb.h>
struct hostent *gethostbyaddr ( const char *addr, size_t len , int family );
```

参数`addr`不是简单的`char *`类型，而是一个真正指向含有`IPv4`或`IPv6`地址的结构`in_addr`或`in6_addr`的指针；`len`是此结构的大小，对于`IPv4`地址为`4`，对于`IPv6`地址为`16`；参数`family`为`AF_INET`或`AF_INET6`。
&emsp;&emsp;`gethostbyaddr`支持`IPv6`，但是当参数是`IPv6`地址时，仍有一些差别：

- 如果`family`是`AF_INET6`，`len`是`16`，且地址是`IPv4`映射的`IPv6`地址，则在域`in_addr.arpa`中查找地址的低`32`位(`IPv4`地址部分)。
- 如果`family`是`AF_INET6`，`len`是`16`，且地址是`IPv4`兼容的`IPv6`地址，则在域`in_addr.arpa`中查找地址的低`32`位(`IPv4`地址部分)。

&emsp;&emsp;代码如下所示：

``` c
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <netdb.h>
​
int main ( int argc, char **argv ) {
    struct in_addr addr;
    struct hostent *phost;
​
    if ( inet_pton ( AF_INET, argv[1], &addr ) <= 0 ) {
        printf ( "inet_pton error:%s\n", strerror ( errno ) );
        return -1;
    }
​
    phost = gethostbyaddr ( ( const char * ) &addr, sizeof ( addr ), AF_INET );
​
    if ( phost == NULL ) {
        printf ( "gethostbyaddr error:%s\n", strerror ( h_errno ) );
        return -1;
    }
​
    printf ( "host name:%s\n", phost->h_name );
    return 0;
}
```

执行结果：

``` bash
$ ./main 127.0.0.1
host name:localhost
```