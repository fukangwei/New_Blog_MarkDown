---
title: Qt之QHostAddress
date: 2019-01-24 14:57:19
categories: Qt语法详解
---
### 简述

&emsp;&emsp;`QHostAddress`类提供一个`IP`地址。这个类提供一种独立于平台和协议的方式来保存`IPv4`和`IPv6`地址。`QHostAddress`通常与`QTcpSocket`、`QTcpServer`、`QUdpSocket`一起使用，来连接到主机或建立一个服务器。
&emsp;&emsp;可以通过`setAddress`来设置一个主机地址，使用`toIPv4Address`、`toIPv6Address`或`toString`来检索主机地址，你可以通过`protocol`来检查协议类型。注意，`QHostAddress`不做`DNS`查询，而`QHostInfo`是有必要的。这个类还支持通用的预定义地址：`Null`、`LocalHost`、`LocalHostIPv6`、`Broadcast`和`Any`。

### 常用接口

&emsp;&emsp;枚举变量为`QHostAddress::SpecialAddress`，取值范围如下：

常量                          | 值  | 描述
------------------------------|-----|----
`QHostAddress::Null`          | `0` | 空地址对象，相当于`QHostAddress`
`QHostAddress::LocalHost`     | `2` | `IPv4`本地主机地址，相当于`QHostAddress("127.0.0.1")`
`QHostAddress::LocalHostIPv6` | `3` | `IPv6`本地主机地址，相当于`QHostAddress("::1")`
`QHostAddress::Broadcast`     | `1` | `Pv4`广播地址，相当于`QHostAddress("255.255.255.255")`
`QHostAddress::AnyIPv4`       | `6` | 意思是`IPv4 any-address`，相当于`QHostAddress("0.0.0.0")`，与该地址绑定的`socket`将只监听`IPv4`接口
`QHostAddress::AnyIPv6`       | `5` | 意思是`IPv6 any-address`，相当于`QHostAddress("::")`，与该地址绑定的`socket`将只监听`IPv6`接口
`QHostAddress::Any`           | `4` | 双`any-address`栈，与该地址绑定的`socket`将侦听`IPv4`和`IPv6`接口

- `bool isLoopback() const`：如果地址是`IPv6`的环回地址，或者`IPv4`的环回地址，则返回`true`。
- `bool isNull() const`：如果主机地址为空(`INADDR_ANY`或`in6addr_any`)，返回`true`。默认的构造函数创建一个空的地址，这个地址对于任何主机或接口是无效的。
- `QAbstractSocket::NetworkLayerProtocol protocol() const`：返回主机地址的网络层协议。
- `QString scopeId() const`：返回`IPv6`地址的范围`ID`。对于`IPv4`地址，如果该地址不包含范围`ID`，则返回一个空字符串。

&emsp;&emsp;`IPv6`的范围`ID`指定非全球`IPv6`地址范围的可达性，限制地址可以被使用的区域。所有`IPv6`地址与这种可达范围相关联。范围`ID`用于消除那些不能保证是全局唯一性的地址。
&emsp;&emsp;当使用`链路本地`或`本地站点`地址的`IPv6`连接，必须指定范围`ID`。对`链路本地`地址来说，范围`ID`通常与接口名称(例如`eth0`、`en1`)或者数目(例如`1`、`2`)相同。

``` cpp
quint32 toIPv4Address() const
quint32 toIPv4Address ( bool *ok ) const
```

返回`IPv4`地址为一个数字。如果地址是`127.0.0.1`，返回值为`2130706433`(即`0x7f000001`)。如果`protocol`是`IPv4Protocol`，该值是有效的；如果是`IPv6Protocol`，并且`IPv6`地址是一个`IPv4`映射的地址，在这种情况下，`ok`将被设置为`true`，否则它将被设置为`false`。

``` cpp
Q_IPV6ADDR toIPv6Address() const
```

返回的`IPv6`地址为`Q_IPV6ADDR`结构，该结构由`16`位无符号字符组成。

``` cpp
Q_IPV6ADDR addr = hostAddr.toIPv6Address(); /* 该地址包含16位无符号字符 */
​
for ( int i = 0; i < 16; ++i ) {
    /* 处理addr[i] */
}
```

如果`protocol`是`IPv6Protocol`，该值是有效的；如果是`IPv4Protocol`，返回地址将是`IPv4`地址映射的`IPv6`地址。

### 使用

&emsp;&emsp;简单应用：构造一个`QHostAddress`，通过`toString`来获取对应的`IP`地址：

``` cpp
QHostAddress address = QHostAddress ( QHostAddress::LocalHost );
QString strIPAddress = address.toString();
```

&emsp;&emsp;获取所有主机地址：`QNetworkInterface`类中提供了一个静态函数`allAddresses`，用于返回一个`QHostAddress`主机地址列表：

``` cpp
QList<QHostAddress> list = QNetworkInterface::allAddresses();
​
foreach ( QHostAddress address, list ) {
    if ( address.isNull() ) { /* 主机地址为空 */
        continue;
    }
​
    QAbstractSocket::NetworkLayerProtocol nProtocol = address.protocol();
    QString strScopeId = address.scopeId();
    QString strAddress = address.toString();
    bool bLoopback = address.isLoopback();
​
    if ( nProtocol == QAbstractSocket::IPv4Protocol ) { /* 如果是IPv4 */
        bool bOk = false;
        quint32 nIPV4 = address.toIPv4Address ( &bOk );
​
        if ( bOk ) {
            qDebug() << "IPV4 : " << nIPV4;
        }
    } else if ( nProtocol == QAbstractSocket::IPv6Protocol ) { /* 如果是IPv6 */
        QStringList IPV6List ( "" );
        Q_IPV6ADDR IPV6 = address.toIPv6Address();
​
        for ( int i = 0; i < 16; ++i ) {
            quint8 nC = IPV6[i];
            IPV6List << QString::number ( nC );
        }
​
        qDebug() << "IPV6: " << IPV6List.join ( " " );
    }
​
    qDebug() << "Protocol: " << nProtocol;
    qDebug() << "ScopeId: " << strScopeId;
    qDebug() << "Address: " << strAddress;
    qDebug() << "IsLoopback: " << bLoopback;
}
```

执行结果：

``` cpp
IPV6: " 254 128 0 0 0 0 0 0 89 150 39 163 131 181 42 231"
Protocol: QAbstractSocket::NetworkLayerProtocol(IPv6Protocol)
ScopeId: "18"
Address: "fe80::5996:27a3:83b5:2ae7%18"
IsLoopback: false
​
IPV4: 3232249857
Protocol: QAbstractSocket::NetworkLayerProtocol(IPv4Protocol)
ScopeId: ""
Address: "192.168.56.1"
IsLoopback: false
​
IPV6: " 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1"
Protocol: QAbstractSocket::NetworkLayerProtocol(IPv6Protocol)
ScopeId: ""
Address: "::1"
IsLoopback: true
​
IPV4: 2130706433
Protocol: QAbstractSocket::NetworkLayerProtocol(IPv4Protocol)
ScopeId: ""
Address: "127.0.0.1"
IsLoopback: true
```

### 过滤

&emsp;&emsp;例如限制本地链路地址范围为`169.254.1.0`至`169.254.254.255`：

``` cpp
bool isLinkLocalAddress ( QHostAddress addr ) {
    quint32 nIPv4 = addr.toIPv4Address();
    quint32 nMinRange = QHostAddress ( "169.254.1.0" ).toIPv4Address();
    quint32 nMaxRange = QHostAddress ( "169.254.254.255" ).toIPv4Address();
​
    if ( ( nIPv4 >= nMinRange ) && ( nIPv4 <= nMaxRange ) ) {
        return true;
    } else {
        return false;
    }
}
```