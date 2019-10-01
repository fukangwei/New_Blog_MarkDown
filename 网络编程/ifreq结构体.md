---
title: ifreq结构体
abbrlink: fc22a71d
date: 2019-01-16 20:33:54
categories: 网络编程
---
&emsp;&emsp;结构原型如下：

``` cpp
struct ifreq {
    #define IFHWADDRLEN 6

    union {
        char ifrn_name[IFNAMSIZ];
    } ifr_ifrn;
​
    union {
        struct sockaddr ifru_addr;
        struct sockaddr ifru_dstaddr;
        struct sockaddr ifru_broadaddr;
        struct sockaddr ifru_netmask;
        struct sockaddr ifru_hwaddr;
        short ifru_flags;
        int ifru_ivalue;
        int ifru_mtu;
        struct ifmap ifru_map;
        char ifru_slave[IFNAMSIZ];
        char ifru_newname[IFNAMSIZ];
        void __user *ifru_data;
        struct if_settings ifru_settings;
    } ifr_ifru;
};
​
#define ifr_name      ifr_ifrn.ifrn_name
#define ifr_hwaddr    ifr_ifru.ifru_hwaddr
#define ifr_addr      ifr_ifru.ifru_addr
#define ifr_dstaddr   ifr_ifru.ifru_dstaddr
#define ifr_broadaddr ifr_ifru.ifru_broadaddr
#define ifr_netmask   ifr_ifru.ifru_netmask
#define ifr_flags     ifr_ifru.ifru_flags
#define ifr_metric    ifr_ifru.ifru_ivalue
#define ifr_mtu       ifr_ifru.ifru_mtu
#define ifr_map       ifr_ifru.ifru_map
#define ifr_slave     ifr_ifru.ifru_slave
#define ifr_data      ifr_ifru.ifru_data
#define ifr_ifindex   ifr_ifru.ifru_ivalue
#define ifr_bandwidth ifr_ifru.ifru_ivalue
#define ifr_qlen      ifr_ifru.ifru_ivalue
#define ifr_newname   ifr_ifru.ifru_newname
#define ifr_settings  ifr_ifru.ifru_settings
```

&emsp;&emsp;`ifreq`结构定义在`/usr/include/net/if.h`中，用来配置`ip`地址、激活接口、配置`MTU`等接口信息的。其中包含了一个接口的名 字和具体内容(它是个共用体，有可能是`IP`地址、广播地址、子网掩码、`MAC`号、`MTU`或其他内容)。`ifreq`包含在`ifconf`结构中，而`ifconf`结构通常是用来保存所有接口的信息的。
&emsp;&emsp;在`Linux`系统中，`ifconfig`命令是通过`ioctl`接口与内核通信。例如当系统管理员输入如下命令来改变接口`eth0`的`MTU`大小：

``` bash
ifconfig eth0 mtu 1250
```

`ifconfig`命令首先打开一个`socket`，然后通过系统管理员输入的参数初始化一个数据结构，并通过`ioctl`调用将数据传送到内核。

``` cpp
struct ifreq data;
fd = socket ( PF_INET, SOCK_DGRAM, 0 );
/* initialize "data" */
err = ioctl ( fd, SIOCSIFMTU, &data ); /* SIOCSIFMTU是命令标识符 */
```