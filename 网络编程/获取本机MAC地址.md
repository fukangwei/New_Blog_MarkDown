---
title: 获取本机MAC地址
abbrlink: '69412986'
date: 2019-01-17 09:01:05
categories: 网络编程
---
&emsp;&emsp;`MAC`地址是唯一的，可以用来识别不同的硬件机。在`Linux`下编写获取本机网卡地址的程序，比较简单的方法是利用套接口(`socket`)和`IO`接口(`ioctl`)函数来获取网卡信息：<!--more-->

``` cpp
#include <sys/socket.h>
#include <sys/ioctl.h>
#include <netinet/if_ether.h>
#include <net/if.h>
#include <linux/sockios.h>

int socket ( int domain, int type, int protocol );
```

- `domain`：表示所使用的协议族。
- `type`：表示套接口的类型。
- `protocol`：表示所使用的协议族中某个特定的协议。

如果函数调用成功，则返回套接口的描述符(`非负整数`)；返回值为`-1`则表明有错误发生。
&emsp;&emsp;利用`socket`函数来获取网卡`MAC`信息时，`domain`使用`AF_INET`，表示采用`Internet`协议族；`type`使用`SOCK_DGRAM`，表示采用数据报类型套接口；`protocol`在这种组合下只有唯一选择，使用`0`填充。
&emsp;&emsp;`IO`控制函数`ioctl`用于对文件进行底层控制，这里的文件包含网卡、终端、磁带机、套接口等软硬件设施，实际的操作来自各个设备自己提供的`ioctl`接口：

``` cpp
int ioctl ( int d, int request, ... );
```

参数`d`取值套接口的描述符，第一个`request`参数指定通过`socket`传输的`IO`类型。本实验可以取值`SIONGIFHWADDR(0x8927)`，表示取硬件地址，其他取值及其含义详见`/usr/includr/linux/sockios.h`。`request`参数用于为实现`IO`控制所必须传入或传出的参数。本实验需要用`ifr`结构传入网卡设备名，并传出`6 Byte`的`MAC`地址。

``` cpp
#include <errno.h>
#include <stdio.h>
#include <sys/socket.h>
#include <net/if_arp.h>
#include <netinet/in.h>
#include <linux/sockios.h>
#include <sys/ioctl.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/ioctl.h>
#include <net/if.h>

int GetMac ( const char *, unsigned char * );
​
int GetMac ( const char *ifname, unsigned char *mac ) {
    int sock, ret;
    struct ifreq ifr;
    sock = socket ( AF_INET, SOCK_STREAM, 0 );
​
    if ( sock < 0 ) {
        perror ( "socket error!\n" );
        return -1;
    }
​
    memset ( &ifr, 0, sizeof ( ifr ) );
    strcpy ( ifr.ifr_name, ifname );
    ret = ioctl ( sock, SIOCGIFHWADDR, &ifr, sizeof ( ifr ) );
​
    if ( ret == 0 ) {
        memcpy ( mac, ifr.ifr_hwaddr.sa_data, 6 );
    } else {
        perror ( "ioctl error!\n" );
    }
​
    close ( sock );
    return ret;
}
​
int main ( int argc, char *argv[] ) {
    int ret;
    char ifname[IFNAMSIZ];
    unsigned char mac[6];
​
    if ( argc == 1 ) {
        /* 系统不同，名字可能会不同，可以使用“ifconfig”命令测试 */
        strcpy ( ifname, "eth1" );
    } else {
        strcpy ( ifname, argv[1] );
    }
​
    memset ( mac, 0, sizeof ( mac ) );
    ret = GetMac ( ifname, mac );
​
    if ( ret == 0 ) {
        printf ( "%s mac address is :[%02X:%02X:%02X:%02X:%02X:%02X]\n", \
                 ifname, mac[0], mac[1], mac[2], mac[3], mac[4], mac[5] );
    } else {
        fprintf ( stderr, "Can,t get %s's mac address\n", ifname );
    }
​
    return 0;
}
```