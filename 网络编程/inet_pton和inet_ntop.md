---
title: inet_pton和inet_ntop
date: 2019-01-17 09:12:00
categories: 网络编程
---
### inet_pton

&emsp;&emsp;`inet_pton`函数用于将`点分十进制`转为`整数`，它是`inet_addr`的扩展：<!--more-->

``` cpp
int inet_pton ( int af, const char *src, void *dst );
```

参数`af`是地址族，支持的地址族如下：

- `af = AF_INET`：`src`为指向`IPv4`地址的字符串。
- `af = AF_INET6`：`src`为指向`IPv6`地址的字符串。

&emsp;&emsp;函数返回值有如下几种情况：

1. 如果转换成功，则返回一个正值。
2. 如果函数出错，则返回一个负值。
3. 如果`af`的地址族和`src`格式不匹配，则返回`0`。

### inet_ntop

&emsp;&emsp;`inet_ntop`函数用于将`整数`转为`点分十进制`：

``` cpp
const char *inet_ntop ( int af, const void *src, char *dst, socklen_t cnt );
```

参数`cnt`是缓存区`dst`的大小，如果缓存区无法存储转换后的地址字符串，则返回`NULL`。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <arpa/inet.h>

int main ( void ) {
    char IPdotdec[20];
    struct in_addr s;
    printf ( "Please input IP address: " );
    scanf ( "%s", IPdotdec );
    inet_pton ( AF_INET, IPdotdec, ( void * ) &s );
    printf ( "inet_pton: 0x%x\n", s.s_addr );
    inet_ntop ( AF_INET, ( void * ) &s, IPdotdec, 16 );
    printf ( "inet_ntop: %s\n", IPdotdec );
}
```