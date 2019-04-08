---
title: TCP服务器
date: 2019-01-17 12:58:23
tags:
---
&emsp;&emsp;`tcp_client.c`如下：

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <netdb.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
​
#define portnumber 3333
​
int main ( int argc, char *argv[] ) {
    int sockfd;
    char buffer[1024];
    struct sockaddr_in server_addr;
    struct hostent *host;
    int nbytes;
​
    /* 使用hostname查询host名字 */
    if ( argc != 2 ) {
        fprintf ( stderr, "Usage:%s hostname \a\n", argv[0] );
        exit ( 1 );
    }
​
    if ( ( host = gethostbyname ( argv[1] ) ) == NULL ) {
        fprintf ( stderr, "Gethostname error\n" );
        exit ( 1 );
    }
​
    /* 客户程序开始建立sockfd描述符 */
    /* AF_INET：Internet；SOCK_STREAM：TCP */
    if ( ( sockfd = socket ( AF_INET, SOCK_STREAM, 0 ) ) == -1 ) {
        fprintf ( stderr, "Socket Error:%s\a\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 客户程序填充服务端的资料 */
    bzero ( &server_addr, sizeof ( server_addr ) ); /* 初始化，置0 */
    server_addr.sin_family = AF_INET; /* IPV4 */
    /* (将本机器上的short数据转化为网络上的short数据)端口号 */
    server_addr.sin_port = htons ( portnumber );
    server_addr.sin_addr = * ( ( struct in_addr * ) host->h_addr ); /* IP地址 */
​
    /* 客户程序发起连接请求 */
    if ( connect ( sockfd, ( struct sockaddr * ) ( &server_addr ), sizeof ( struct sockaddr ) ) == -1 ) {
        fprintf ( stderr, "Connect Error:%s\a\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 连接成功了 */
    if ( ( nbytes = read ( sockfd, buffer, 1024 ) ) == -1 ) {
        fprintf ( stderr, "Read Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    buffer[nbytes] = '\0';
    printf ( "I have received:%s\n", buffer );
    /* 结束通讯 */
    close ( sockfd );
    exit ( 0 );
}
```

tcp_server.c：

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <netdb.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
​
#define portnumber 3333
​
int main ( int argc, char *argv[] ) {
    int sockfd, new_fd;
    struct sockaddr_in server_addr;
    struct sockaddr_in client_addr;
    int sin_size;
    char hello[] = "Hello! Are You Fine?\n";
​
    /* 服务器端开始建立sockfd描述符 */
    /* AF_INET：IPV4；SOCK_STREAM：TCP */
    if ( ( sockfd = socket ( AF_INET, SOCK_STREAM, 0 ) ) == -1 ) {
        fprintf ( stderr, "Socket error:%s\n\a", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 服务器端填充sockaddr结构 */
    bzero ( &server_addr, sizeof ( struct sockaddr_in ) ); /* 初始化，置0 */
    server_addr.sin_family = AF_INET; /* Internet */
    /* (将本机器上的long数据转化为网络上的long数据)和任何主机通信，
       INADDR_ANY表示可以接收任意IP地址的数据，即绑定到所有的IP */
    server_addr.sin_addr.s_addr = htonl ( INADDR_ANY );
    /* (将本机器上的short数据转化为网络上的short数据)端口号 */
    server_addr.sin_port = htons ( portnumber );
​
    /* 捆绑sockfd描述符到IP地址 */
    if ( bind ( sockfd, ( struct sockaddr * ) ( &server_addr ), sizeof ( struct sockaddr ) ) == -1 ) {
        fprintf ( stderr, "Bind error:%s\n\a", strerror ( errno ) );
        exit ( 1 );
    }
​
    if ( listen ( sockfd, 5 ) == -1 ) { /* 设置允许连接的最大客户端数 */
        fprintf ( stderr, "Listen error:%s\n\a", strerror ( errno ) );
        exit ( 1 );
    }
​
    while ( 1 ) {
        sin_size = sizeof ( struct sockaddr_in );
​
        /* 服务器阻塞，直到客户程序建立连接 */
        if ( ( new_fd = accept ( sockfd, ( struct sockaddr * ) ( &client_addr ), &sin_size ) ) == -1 ) {
            fprintf ( stderr, "Accept error:%s\n\a", strerror ( errno ) );
            exit ( 1 );
        }
​
        /* 将网络地址转换成“.”字符串格式 */
        fprintf ( stderr, "Server get connection from %s\n", inet_ntoa ( client_addr.sin_addr ) );
​
        if ( write ( new_fd, hello, strlen ( hello ) ) == -1 ) {
            fprintf ( stderr, "Write Error:%s\n", strerror ( errno ) );
            exit ( 1 );
        }
​
        close ( new_fd ); /* 这个通讯已经结束 */
        /* 循环下一个 */
    }
​
    close ( sockfd ); /* 结束通讯 */
    exit ( 0 );
}
```

并发服务器版本`tcp_server.c`如下：

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <netdb.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include "pthread.h"
​
#define portnumber 3333
const char hello[] = "Hello! Are You Fine?\n";
​
void *thread_server ( void *arg ) {
    int new_fd = * ( int * ) arg;
    printf ( "new_fd is %d\n", new_fd ); /* 打印建立连接的客户端产生的套接字 */
​
    if ( write ( new_fd, hello, strlen ( hello ) ) == -1 ) {
        fprintf ( stderr, "Write Error:%s\n", strerror ( errno ) );
        exit ( 1 );
    }
​
    close ( new_fd );
}
​
int main ( int argc, char *argv[] ) {
    int sockfd, new_fd;
    struct sockaddr_in server_addr;
    struct sockaddr_in client_addr;
    int sin_size;
​
    /* 服务器端开始建立sockfd描述符 */
    /* AF_INET：IPV4；SOCK_STREAM：TCP */
    if ( ( sockfd = socket ( AF_INET, SOCK_STREAM, 0 ) ) == -1 ) {
        fprintf ( stderr, "Socket error:%s\n\a", strerror ( errno ) );
        exit ( 1 );
    }
​
    /* 服务器端填充sockaddr结构 */
    bzero ( &server_addr, sizeof ( struct sockaddr_in ) ); /* 初始化，置0 */
    server_addr.sin_family = AF_INET; /* Internet */
    /* (将本机器上的long数据转化为网络上的long数据)和任何主机通信，
       INADDR_ANY表示可以接收任意IP地址的数据，即绑定到所有的IP */
    server_addr.sin_addr.s_addr = htonl ( INADDR_ANY );
    /* (将本机器上的short数据转化为网络上的short数据)端口号 */
    server_addr.sin_port = htons ( portnumber );
​
    /* 捆绑sockfd描述符到IP地址 */
    if ( bind ( sockfd, ( struct sockaddr * ) ( &server_addr ), sizeof ( struct sockaddr ) ) == -1 ) {
        fprintf ( stderr, "Bind error:%s\n\a", strerror ( errno ) );
        exit ( 1 );
    }
​
    if ( listen ( sockfd, 5 ) == -1 ) { /* 设置允许连接的最大客户端数 */
        fprintf ( stderr, "Listen error:%s\n\a", strerror ( errno ) );
        exit ( 1 );
    }
​
    while ( 1 ) {
        pthread_t tid; /* 线程的ID号 */
        sin_size = sizeof ( struct sockaddr_in );
​
        /* 服务器阻塞，直到客户程序建立连接 */
        if ( ( new_fd = accept ( sockfd, ( struct sockaddr * ) ( &client_addr ), &sin_size ) ) == -1 ) {
            fprintf ( stderr, "Accept error:%s\n\a", strerror ( errno ) );
            exit ( 1 );
        }
​
        /* 将网络地址转换成“.”格式字符串 */
        fprintf ( stderr, "Server get connection from %s\n", inet_ntoa ( client_addr.sin_addr ) );
​
        if ( ( pthread_create ( &tid, NULL, thread_server, &new_fd ) ) == -1 ) {
            perror ( "pthread_creat error!\n" );
            close ( sockfd );
            close ( new_fd );
            return 1;
        }
​
        /* 循环下一个 */
    }
​
    close ( sockfd ); /* 结束通讯 */
    exit ( 0 );
}
```

---

&emsp;&emsp;`tcp_client_ipv6.c`如下：

``` cpp
#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <sys/socket.h>
#include <resolv.h>
#include <stdlib.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
​
#define MAXBUF 1024
#define SEND_PORT 8888
​
int main ( int argc, char **argv ) {
    int sockfd, len;
    struct sockaddr_in6 dest;
    char buffer[MAXBUF + 1];
​
    if ( ( sockfd = socket ( AF_INET6, SOCK_STREAM, 0 ) ) < 0 ) {
        perror ( "Socket" );
        exit ( errno );
    }
​
    printf ( "socket created\n" );
    bzero ( &dest, sizeof ( dest ) );
    dest.sin6_family = AF_INET6;
    dest.sin6_port = htons ( SEND_PORT );
​
    if ( inet_pton ( AF_INET6, argv[1], &dest.sin6_addr ) < 0 ) {
        perror ( argv[1] );
        exit ( errno );
    }
​
    printf ( "address created\n" );
​
    if ( connect ( sockfd, ( struct sockaddr * ) &dest, sizeof ( dest ) ) != 0 ) {
        perror ( "Connect " );
        exit ( errno );
    }
​
    printf ( "server connected\n" );
    bzero ( buffer, MAXBUF + 1 );
    len = recv ( sockfd, buffer, MAXBUF, 0 );
​
    if ( len > 0 ) {
        printf ( "I receieve %s\r\n", buffer );
    }
    else {
        printf ( "%d %s\n", errno, strerror ( errno ) );
    }
​
    close ( sockfd );
    return 0;
}
```

&emsp;&emsp;`tcp_server_ipv6.c`如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <sys/wait.h>
#include <unistd.h>
#include <arpa/inet.h>
​
#define MAXBUF 1024
#define LISTEN_PORT 8888
#define LISTEN_NUM  10
​
int main ( int argc, char **argv ) {
    int sockfd, new_fd;
    socklen_t len;
    struct sockaddr_in6 my_addr, their_addr;
    char buf[MAXBUF + 1];
​
    if ( ( sockfd = socket ( PF_INET6, SOCK_STREAM, 0 ) ) == -1 ) {
        perror ( "socket error" );
        exit ( 1 );
    }
    else {
        printf ( "socket created\r\n" );
    }
​
    bzero ( &my_addr, sizeof ( my_addr ) );
    my_addr.sin6_family = PF_INET6;
    my_addr.sin6_port = htons ( LISTEN_PORT );
    my_addr.sin6_addr = in6addr_any;
​
    if ( bind ( sockfd, ( struct sockaddr * ) &my_addr, sizeof ( struct sockaddr_in6 ) ) == -1 ) {
        perror ( "bind error" );
        exit ( 1 );
    }
    else {
        printf ( "binded\r\n" );
    }
​
    if ( listen ( sockfd, LISTEN_NUM ) == -1 ) {
        perror ( "listen error" );
        exit ( 1 );
    }
    else {
        printf ( "begin listen\r\n" );
    }
​
    while ( 1 ) {
        len = sizeof ( struct sockaddr );
​
        if ( ( new_fd = accept ( sockfd, ( struct sockaddr * ) &their_addr, &len ) ) == -1 ) {
            perror ( "accept error" );
            exit ( errno );
        }
        else {
            printf ( "server: got connection from %s, port %d, socket %d\r\n", \
                     inet_ntop ( AF_INET6, &their_addr.sin6_addr, buf, sizeof ( buf ) ), \
                     their_addr.sin6_port, new_fd );
        }
​
        bzero ( buf, MAXBUF + 1 );
        strncpy ( buf, "hello! Little", strlen ( "hello! Little" ) );
        len = send ( new_fd, buf, strlen ( buf ), 0 );
​
        if ( len < 0 ) {
            perror ( "send error!" );
        }
        else {
            printf ( "send succeed\r\n" );
        }
    }
​
    close ( sockfd );
    return 0;
}
```