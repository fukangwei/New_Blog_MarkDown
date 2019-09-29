---
title: Qt之QNetworkAddressEntry
categories: Qt语法详解
abbrlink: c391d68f
date: 2019-01-03 09:04:44
---
&emsp;&emsp;`QNetworkAddressEntry`类由网络接口支持，存储了`IP`地址、子网掩码和广播地址。每个网络接口可以包含零个或多个`IP`地址，进而可以关联到一个子网掩码`和/或`一个广播地址(取决于操作系统的支持)。常用接口如下：

- `QHostAddress broadcast() const`：返回`IPv4`地址和子网掩码相关联的广播地址。对于`IPv6`地址来说，返回的总是空，因为广播的概念已被抛弃，多播的概念开始兴起。
- `QHostAddress ip() const`：返回一个网络接口中存在的`IPv4`或`IPv6`地址。
- `QHostAddress netmask() const`：返回与`IP`地址相关联的子网掩码。子网掩码是一个`IP`地址的形式表示，例如`255.255.0.0`。对于`IPv6`地址，前缀长度被转换成一个地址，其中设置为`1`的位数等于前缀长度。前缀长度为`64`位(最常见的值)，子网掩码将被表示为一个地址为`FFFF:FFFF:FFFF:FFFF::`的`QHostAddress`。
- `int prefixLength() const`：返回此`IP`地址的前缀长度。前缀长度和子网掩码中设置为`1`的位数相匹配。`IPv4`地址的值在`0`至`32`之间，`IPv6`地址的值在`0`至`128`之间。如果前缀长度不能确定，则返回`0`(即`netmask`返回一个空的`QHostAddress`)。例如`255.255.240.0`转换成二进制为`11111111_11111111_11110000_00000000`，那么前缀长度就是`8 * 2 + 4 = 20`(`1`的个数)。`ffff:ffff:ffff:ffff::`转换成二进制为`1111111111111111_1111111111111111_1111111111111111_1111111111111111`，那么前缀长度就是`16 * 4 = 64`(`1`的个数)。

&emsp;&emsp;`QNetworkInterface`类中提供了一个便利的静态函数`allInterfaces`，用于返回所有的网络接口：

``` cpp
QList<QNetworkInterface> list = QNetworkInterface::allInterfaces();
​
foreach ( QNetworkInterface netInterface, list ) {
    QList<QNetworkAddressEntry> entryList = netInterface.addressEntries();
​
    foreach ( QNetworkAddressEntry entry, entryList ) { /* 遍历每一个IP地址 */
        qDebug() << "IP Address:" << entry.ip().toString(); /* IP地址 */
        qDebug() << "Netmask:" << entry.netmask().toString(); /* 子网掩码 */
        qDebug() << "Broadcast:" << entry.broadcast().toString(); /* 广播地址 */
        qDebug() << "Prefix Length:" << entry.prefixLength(); /* 前缀长度 */
    }
}
```

通过遍历每一个网络接口`QNetworkInterface`，根据其`addressEntries`函数，可以很容易地获取到所有的`QNetworkAddressEntry`，然后通过`ip`、`netmask`、`broadcast`函数获取对应的`IP`地址、子网掩码以及广播地址。执行结果：

``` cpp
IP Address: "fe80::550c:ab19:fb48:1c9%15"
Netmask: "ffff:ffff:ffff:ffff::"
Broadcast: ""
Prefix Length: 64
​
IP Address: "169.254.1.201"
Netmask: ""
Broadcast: ""
Prefix Length: -1
​
IP Address: "fe80::d086:8566:6065:8954%11"
Netmask: "ffff:ffff:ffff:ffff::"
Broadcast: ""
Prefix Length: 64
​
IP Address: "172.18.4.165"
Netmask: "255.255.240.0"
Broadcast: "172.18.15.255"
Prefix Length: 20
​
IP Address: "::1"
Netmask: "ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff"
Broadcast: ""
Prefix Length: 128
​
IP Address: "127.0.0.1"
Netmask: ""
Broadcast: ""
Prefix Length: -1
```