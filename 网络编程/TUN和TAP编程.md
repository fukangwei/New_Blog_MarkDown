---
title: TUN和TAP编程
abbrlink: 85998e3a
date: 2019-01-17 13:45:20
categories: 网络编程
---
&emsp;&emsp;`TUN/TAP`虚拟网络设备为用户空间程序提供了网络数据包的发送和接收能力。它既可以当做点对点设备`TUN`，也可以当做以太网设备`TAP`。实际上，不仅`Linux`支持`TUN/TAP`虚拟网络设备，其他`UNIX`也支持。<!--more-->

### 原理简介

&emsp;&emsp;`TUN/TAP`虚拟网络设备的原理比较简单，它在`Linux`内核中添加了一个`TUN/TAP`虚拟网络设备的驱动程序和一个与之相关联的字符设备`/dev/net/tun`，字符设备`tun`作为用户空间和内核空间交换数据的接口。当内核将数据包发送到虚拟网络设备时，数据包被保存在设备相关的一个队列中，直到用户空间程序通过打开的字符设备`tun`的描述符读取时，它才会被拷贝到用户空间的缓冲区中，其效果就相当于数据包直接发送到了用户空间。通过系统调用`write`发送数据包时其原理与此类似。
&emsp;&emsp;值得注意的是，一次`read`系统调用，有且只有一个数据包被传送到用户空间，并且当用户空间的缓冲区比较小时，数据包将被截断，剩余部分将永久地消失。`write`系统调用与`read`类似，每次只发送一个数据包。所以在编写此类程序的时候，请用足够大的缓冲区，直接调用系统调用`read/write`，避免采用`C`语言的带缓存的`IO`函数。

### 准备工作

&emsp;&emsp;首先你需要一个能工作的`Linux`操作系统，并且内核支持`TUN/TAP`虚拟网络设备。如果没有，请在内核中选中：

``` bash
Device Drivers => Network device support => Universal TUN/TAP device driver support
```

你可以选择编译进内核或者是编译成模块，然后重新编译内核并用新内核启动。如果你编译的是模块，那么在下步开始之前，你需要手工加载它：

``` bash
modprobe tun
```

### 开始编程

&emsp;&emsp;代码如下：

``` c
#include <linux/if_tun.h>
​
int tun_create ( char *dev, int flags ) {
    struct ifreq ifr;
    int fd, err;
    assert ( dev != NULL );
​
    if ( ( fd = open ( "/dev/net/tun", O_RDWR ) ) < 0 ) {
        return fd;
    }
​
    memset ( &ifr, 0, sizeof ( ifr ) );
    ifr.ifr_flags |= flags;
​
    if ( *dev != '\0' ) {
        strncpy ( ifr.ifr_name, dev, IFNAMSIZ );
    }
​
    if ( ( err = ioctl ( fd, TUNSETIFF, ( void * ) &ifr ) ) < 0 ) {
        close ( fd );
        return err;
    }
​
    strcpy ( dev, ifr.ifr_name );
    return fd;
}
```

为了使用`TUN/TAP`设备，必须包含特定的头文件`linux/if_tun.h`。第`8`行打开了字符设备`/dev/net/tun`，接下来需要为`ioctl`的`TUNSETIFF`命令初始化一个结构体`ifr`，一般只需要关心其中的两个成员`ifr_name`和`ifr_flags`：

- `ifr_name`定义了要创建或者是打开的虚拟网络设备的名字，如果它为空或者是此网络设备不存在，内核将新建一个虚拟网络设备，并返回新建的虚拟网络设备的名字，同时文件描述符`fd`也将和此网络设备建立起关联。如果并没有指定网络设备的名字，内核将根据其类型自动选择`tunXX`和`tapXX`作为其名字。
- `ifr_flags`用来描述网络设备的一些属性，比如说是点对点设备还是以太网设备。详细的选项解释如下：

1. `IFF_TUN`：创建一个点对点设备。
2. `IFF_TAP`：创建一个以太网设备。
3. `IFF_NO_PI`：不包含包信息，默认的每个数据包当传到用户空间时，都将包含一个附加的包头来保存包信息。
4. `IFF_ONE_QUEUE`：采用单一队列模式，即当数据包队列满的时候，由虚拟网络设备自已丢弃以后的数据包直到数据包队列再有空闲。

配置的时候，`IFF_TUN`和`IFF_TAP`必须选择一个，其他选项则可任意组合。其中，`IFF_NO_PI`没有开启时，所附加的包信息头如下：

``` c
struct tun_pi {
    unsigned short flags;
    unsigned short proto;
};
```

目前`flags`只在收取数据包的时候有效，当它的`TUN_PKT_STRIP`标志被置时，表示当前的用户空间缓冲区太小，以致数据包被截断。`proto`成员表示`发送/接收`的数据包的协议。
&emsp;&emsp;上面代码中的文件描述符`fd`除了支持`TUN_SETIFF`和其他的常规`ioctl`命令外，还支持以下命令：

- `TUNSETNOCSUM`：不做校验和校验，参数为`int`型的`bool`值。
- `TUNSETPERSIST`：把对应网络设备设置成持续模式，默认的虚拟网络设备，当其相关的文件符被关闭时，也将会伴随着与之相关的路由等信息同时消失。如果设置成持续模式，那么它将会被保留供以后使用，参数为`int`型的`bool`值。
- `TUNSETOWNER`：设置网络设备的属主，参数类型为`uid_t`。
- `TUNSETLINK`：设置网络设备的链路类型，此命令只有在虚拟网络设备关闭的情况下有效，参数为`int`型。

``` cpp
#include "sys/types.h"
#include "sys/stat.h"
#include "fcntl.h"
#include "unistd.h"
#include "string.h"
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>
#include <net/if.h>
#include <sys/ioctl.h>
​
int main ( int argc, char *argv[] ) {
    int tun, ret;
    char tun_name[IFNAMSIZ];
    unsigned char buf[4096];
    tun_name[0] = '\0';
    tun = tun_create ( tun_name, IFF_TUN | IFF_NO_PI );
​
    if ( tun < 0 ) {
        perror ( "tun_create" );
        return 1;
    }
​
    printf ( "TUN name is %s\n", tun_name );
​
    while ( 1 ) {
        unsigned char ip[4];
        ret = read ( tun, buf, sizeof ( buf ) );
​
        if ( ret < 0 ) {
            break;
        }
​
        memcpy ( ip, &buf[12], 4 );
        memcpy ( &buf[12], &buf[16], 4 );
        memcpy ( &buf[16], ip, 4 );
        buf[20] = 0;
        * ( ( unsigned short * ) &buf[22] ) += 8;
        printf ( "read %d bytes\n", ret );
        ret = write ( tun, buf, ret );
        printf ( "write %d bytes\n", ret );
    }
​
    return 0;
}
```

以上代码简单地处理了`ICMP`的`ECHO`包，并回应以`ECHO REPLY`。
&emsp;&emsp;首先运行这个程序(在`root`权限下)：

``` cpp
$ ./a.out
TUN name is tun0
```

接着在另外一个终端运行如下命令:

``` cpp
root@gentux linux-2.6.15-gentoo# ifconfig tun0 0.0.0.0 up
root@gentux linux-2.6.15-gentoo# route add 10.10.10.1 dev tun0
root@gentux linux-2.6.15-gentoo# ping 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=1.09 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=5.18 ms
64 bytes from 10.10.10.1: icmp_seq=3 ttl=64 time=3.37 ms
​
--- 10.10.10.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2011ms
rtt min/avg/max/mdev = 1.097/3.218/5.181/1.671 ms
```

于是我们顺利地接收到了回应包。这时切回到前一个终端下，看到如下执行结果：

``` cpp
read 84 bytes
write 84 bytes
read 84 bytes
write 84 bytes
read 84 bytes
write 84 bytes
```