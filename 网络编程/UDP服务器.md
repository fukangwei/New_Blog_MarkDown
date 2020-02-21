---
title: UDP服务器
abbrlink: 47e5811b
date: 2019-01-16 21:28:24
categories: 网络编程
---
&emsp;&emsp;`udp_client.c`如下：<!--more-->

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <unistd.h>
#include <netdb.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <arpa/inet.h>
​
#define SERVER_PORT  8888
#define MAX_BUF_SIZE 1024

void udpc_requ ( int sockfd, const struct sockaddr_in *addr, int len ) {
    char buffer[MAX_BUF_SIZE];
    int n;

    while ( 1 ) {
        /* 从键盘读入，写到服务端 */
        printf ( "Please input char:\r\n" );
        fgets ( buffer, MAX_BUF_SIZE, stdin );
        sendto ( sockfd, buffer, strlen ( buffer ), 0, addr, len );
        bzero ( buffer, MAX_BUF_SIZE );
    }
}
​
int main ( int argc, char **argv ) {
    int sockfd;
    struct sockaddr_in addr;
​
    if ( argc != 2 ) {
        fprintf ( stderr, "Usage:%s server_ip\n", argv[0] );
        exit ( 1 );
    }
​
    /* 建立sockfd描述符 */
    sockfd = socket ( AF_INET, SOCK_DGRAM, 0 );
​
    if ( sockfd < 0 ) {
        fprintf ( stderr, "Socket Error:%s\r\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 填充服务端的资料 */
    bzero ( &addr, sizeof ( struct sockaddr_in ) );
    addr.sin_family = AF_INET;
    addr.sin_port = htons ( SERVER_PORT );
​
    /* inet_aton函数用于把字符串型的IP地址转化成网络二进制数字 */
    if ( inet_aton ( argv[1], &addr.sin_addr ) < 0 ) {
        fprintf ( stderr, "Ip error:%s\r\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    udpc_requ ( sockfd, &addr, sizeof ( struct sockaddr_in ) ); /* 进行读写操作 */
    close ( sockfd );
}
```

&emsp;&emsp;`udp_server.c`如下：

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <unistd.h>
#include <netdb.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <arpa/inet.h>
​
#define SERVER_PORT  8888
#define MAX_MSG_SIZE 1024
​
void udps_respon ( int sockfd ) {
    struct sockaddr_in addr;
    int addrlen, n;
    char msg[MAX_MSG_SIZE];

    while ( 1 ) {
        bzero ( msg, sizeof ( msg ) ); /* 初始化，清零 */
        addrlen = sizeof ( struct sockaddr );
        /* 从客户端接收消息 */
        n = recvfrom ( sockfd, msg, MAX_MSG_SIZE, 0, ( struct sockaddr * ) &addr, &addrlen );
        msg[n] = 0;
        fprintf ( stdout, "Server have received %s\r\n", msg ); /* 显示服务端已经收到了信息 */
    }
}
​
int main ( void ) {
    int sockfd;
    struct sockaddr_in addr;
    /* 服务器端开始建立socket描述符 */
    sockfd = socket ( AF_INET, SOCK_DGRAM, 0 );
​
    if ( sockfd < 0 ) {
        fprintf ( stderr, "Socket Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 服务器端填充sockaddr结构 */
    bzero ( &addr, sizeof ( struct sockaddr_in ) );
    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = htonl ( INADDR_ANY );
    addr.sin_port = htons ( SERVER_PORT );
​
    /* 捆绑sockfd描述符 */
    if ( bind ( sockfd, ( struct sockaddr * ) &addr, sizeof ( struct sockaddr_in ) ) < 0 ) {
        fprintf ( stderr, "Bind Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    udps_respon ( sockfd ); /* 进行读写操作 */
    close ( sockfd );
}
```

&emsp;&emsp;`uip_client_ipv6.c`如下：

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <unistd.h>
#include <netdb.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <arpa/inet.h>
​
#define SERVER_PORT  8888
#define MAX_BUF_SIZE 1024
​
void udpc_requ ( int sockfd, const struct sockaddr_in6 *addr, int len ) {
    char buffer[MAX_BUF_SIZE];
    int n;
​
    while ( 1 ) {
        /* 从键盘读入，写到服务端 */
        printf ( "Please input char:\n" );
        fgets ( buffer, MAX_BUF_SIZE, stdin );
        sendto ( sockfd, buffer, strlen ( buffer ), 0, ( struct sockaddr * ) addr, len );
        bzero ( buffer, MAX_BUF_SIZE );
    }
}
​
int main ( int argc, char **argv ) {
    int sockfd;
    struct sockaddr_in6 addr;
​
    if ( argc != 2 ) {
        fprintf ( stderr, "Usage:%s server_ip\n", argv[0] );
        exit ( 1 );
    }
​
    /* 建立sockfd描述符 */
    sockfd = socket ( AF_INET6, SOCK_DGRAM, 0 );
​
    if ( sockfd < 0 ) {
        fprintf ( stderr, "Socket Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 填充客户端的资料 */
    bzero ( &addr, sizeof ( struct sockaddr_in6 ) );
    addr.sin6_family = AF_INET6;
    addr.sin6_port = htons ( SERVER_PORT );
    inet_pton ( AF_INET6, argv[1], &addr.sin6_addr );
    udpc_requ ( sockfd, &addr, sizeof ( struct sockaddr_in6 ) ); /* 进行读写操作 */
    close ( sockfd );
}
```

&emsp;&emsp;`uip_server_ipv6.c`如下：

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <unistd.h>
#include <netdb.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <arpa/inet.h>
​
#define SERVER_PORT  8888
#define MAX_MSG_SIZE 1024

void udps_respon ( int sockfd ) {
    struct sockaddr_in6 addr;
    int addrlen, n;
    char msg[MAX_MSG_SIZE];
​
    while ( 1 ) {
        bzero ( msg, sizeof ( msg ) ); /* 初始化，清零 */
        addrlen = sizeof ( struct sockaddr_in6 );
        /* 从客户端接收消息 */
        n = recvfrom ( sockfd, msg, MAX_MSG_SIZE, 0, ( struct sockaddr * ) &addr, &addrlen );
        msg[n] = 0;
        fprintf ( stdout, "Server have received %s", msg ); /* 显示服务端已经收到了信息 */
    }
}
​
int main ( void ) {
    int sockfd;
    struct sockaddr_in6 addr;
    /* 服务器端开始建立socket描述符 */
    sockfd = socket ( AF_INET6, SOCK_DGRAM, 0 );
​
    if ( sockfd < 0 ) {
        fprintf ( stderr, "Socket Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 服务器端填充sockaddr结构 */
    bzero ( &addr, sizeof ( struct sockaddr_in6 ) );
    addr.sin6_family = AF_INET6;
    addr.sin6_addr = in6addr_any;
    addr.sin6_port = htons ( SERVER_PORT );
​
    /* 捆绑sockfd描述符 */
    if ( bind ( sockfd, ( struct sockaddr * ) &addr, sizeof ( struct sockaddr_in6 ) ) < 0 ) {
        fprintf ( stderr, "Bind Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    udps_respon ( sockfd ); /* 进行读写操作 */
    close ( sockfd );
}
```