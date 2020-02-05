---
title: IP数据报首部checksum
abbrlink: b0023368
date: 2019-01-16 21:35:28
categories: 网络编程
---
&emsp;&emsp;`IP`数据报`checksum`的计算方法为：将校验和字段置为`0`；对首部中每个`16`位字进行二进制反码求和；对第二步得到的和再取反码，就得到`checksum`，写入校验和字段中。<!--more-->
&emsp;&emsp;抓取一个`IP`数据包，取出`IP`数据报报头部分，数据为`45 00 00 30 80 4c 40 00 80 06 b5 2e d3 43 11 7b cb 51 15 3d`，下面来计算一下校验和：
&emsp;&emsp;1. 将校验和字段置为`0`：将`b5 2e`置为`00 00`，得到`45 00 00 30 80 4c 40 00 80 06 00 00 d3 43 11 7b cb 51 15 3d`。
&emsp;&emsp;2. 反码求和：`4500 + 0030 + 804c + 4000 + 8006 + 0000 + d343 + 117b + cb51 + 153d = 34ace`，将进位(`3`)加到低`16`位(`4ace`)上，得到`0003 + 4ace = 4ad1`。
&emsp;&emsp;3. 取反码：将`4ad1`取反，得到`checksum`为`b52e`。
&emsp;&emsp;接收到`IP`数据报后对其进行校验，其方法为：对首部中每个`16 bit`进行二进制反码求和；将第一步得到的和再取反码，看是否为`0`。
&emsp;&emsp;假设接收到的`IP`数据报首部为`45 00 00 30 80 4c 40 00 80 06 b5 2e d3 43 11 7b cb 51 15 3d`，下面来验证一下：
&emsp;&emsp;1. 反码求和：`4500 + 0030 + 804c + 4000 + 8006 + b52e + d343 + 117b + cb51 + 153d = 3fffc`，`0003 + fffc = ffff`。
&emsp;&emsp;2. 取反码：`~ffff = 0`，为`0`意味着通过校验。
&emsp;&emsp;求`IP`数据报首部校验码代码如下：

``` cpp
#include <stdio.h>
​
void main ( void ) {
    int buff[10] = {0x4500, 0x0030, 0x804c, 0x4000, 0x8006, \
                    0x0000, 0xd343, 0x117b, 0xcb51, 0x153d};
    int checksum = 0, i = 0;
​
    for ( i = 0; i < 10; i++ ) {
        checksum += buff[i];
    }
​
    printf ( "checksum = %x\n", checksum );
    checksum = ( checksum >> 16 ) + ( checksum & 0xffff );
    checksum += ( checksum >> 16 );
    checksum = 0xffff - checksum;
    printf ( "checksum = %04x\n", checksum );
}
```

---

### IPv6与IPv4的“ip header checksum”

&emsp;&emsp;`IPv6`认为`Datalink layer`和`Transport layer`都有校验的功能，将校验的工作交给这两层就好了。`IPv4`路由器在进行转发时，会修改`TTL`等数值，并且要重新计算`checksum`，因此增加了路由器的负担。