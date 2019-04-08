---
title: Qt之QNetworkInterface
date: 2019-01-23 16:56:20
categories: Qt语法详解
---
### 简述

&emsp;&emsp;`QNetworkInterface`类负责提供主机的`IP`地址和网络接口的列表，它表示了当前程序正在运行时与主机绑定的一个网络接口。每个网络接口可能包含`0`个或多个`IP`地址，每个`IP`地址都可选择性地与一个子网掩码`和/或`一个广播地址相关联。这样的列表可以通过`addressEntries`方法获得。当子网掩码或者广播地址不必要时，可以使用`allAddresses`函数来仅仅获得`IP`地址。`QNetworkInterface`使用`hardwareAddress`方法获取接口的硬件地址。

### 常用接口

&emsp;&emsp;枚举值为`QNetworkInterface::InterfaceFlag`，标识为`QNetworkInterface::InterfaceFlags`。它用于指定网络接口相关的标识，可能的值为：

常量                               | 值      | 描述
-----------------------------------|--------|---------
`QNetworkInterface::IsUp`          | `0x1`  | 网络接口处于活动状态
`QNetworkInterface::IsRunning`     | `0x2`  | 网络接口已分配资源
`QNetworkInterface::CanBroadcast`  | `0x4`  | 网络接口工作在广播模式
`QNetworkInterface::IsLoopBack`    | `0x8`  | 网络接口是环回接口
`QNetworkInterface::IsPointToPoint`| `0x10` | 网络接口是一个点对点接口
`QNetworkInterface::CanMulticast`  | `0x20` | 网络接口支持多播

注意，一个网络接口不能既是`broadcast-based`又是`point-to-point`。`InterfaceFlags`类型是一个`QFlags`类型定义，它存储一个或`InterfaceFlag`的组合值。

- `QList<QHostAddress> allAddresses() [static]`: 函数返回主机上面发现的所有`IP`地址，相当于`allInterfaces`。返回的所有对象调用`addressEntries`来获取`QHostAddress`对象列表，然后对每一个对象调用`QHostAddress::ip`方法。
- `QList<QNetworkInterface> allInterfaces() [static]`: 返回主机上找到的所有的网络接口的列表。在失败情况下，它会返回一个空列表。
- `QList<QNetworkAddressEntry> addressEntries() const`: 返回`IP`地址列表，这个列表具备与其`IP`地址相关的网络掩码和广播地址。如果不需要子网掩码或广播地址的信息，可以调用`allAddresses`函数来只获取`IP`地址。
- `InterfaceFlags flags() const`: 返回与此网络接口关联的标志。
- `QString hardwareAddress() const`: 返回此接口的底层硬件地址。在以太网接口上，这是表示`MAC`地址的字符串，用冒号分隔。
- `QString humanReadableName() const`: 如果名称可确定，在`Windows`上返回网络接口的人类可读的名称，例如`本地连接`；如果不能，这个函数返回值与`name`相同。用户可以在`Windows`控制面板中修改人类可读的名称，因此它可以在程序的执行过程中变化。在`Unix`上，此函数目前返回值总是和`name`相同，因为`Unix`系统不存储人类可读的名称的配置。
- `bool isValid() const`: 如果此`QNetworkInterface`对象包含一个的有效的网络接口，则返回`true`。
- `QString QNetworkInterface::name() const`: 返回网络接口的名称。在`Unix`系统中，这是一个包含接口的类型和任选的序列号的字符串，例如`eth0`、`lo`或者`pcn0`；在`Windows`中，这是一个内部`ID`，用户不能更改。

### 获取所有IP地址

&emsp;&emsp;静态函数`allAddresses`可以返回一个`QHostAddress`地址列表(只能获取`IP`地址，没有子网掩码和广播地址的信息)：

``` cpp
QList<QHostAddress> list = QNetworkInterface::allAddresses();
​
foreach ( QHostAddress address, list ) {
    if ( !address.isNull() ) {
        qDebug() << "Address: " << address.toString();
    }
}
```

执行结果：

``` cpp
Address: "fe80::550c:ab19:fb48:1c9%15"
Address: "169.254.1.201"
Address: "fe80::d086:8566:6065:8954%11"
Address: "172.18.4.165"
Address: "fe80::f864:a962:7219:f98e%16"
Address: "192.168.17.1"
Address: "fe80::8169:691f:148e:d3cb%17"
Address: "192.168.178.1"
Address: "fe80::5996:27a3:83b5:2ae7%18"
Address: "192.168.56.1"
Address: "::1"
Address: "127.0.0.1"
```

### 获取网络接口列表

&emsp;&emsp;静态函数`allInterfaces`可以返回一个`QNetworkInterface`网络接口列表(通过`QNetworkAddressEntry`，可以获取`IP`地址、子网掩码和广播地址等信息)。

``` cpp
QList<QNetworkInterface> list = QNetworkInterface::allInterfaces();
​
foreach ( QNetworkInterface netInterface, list ) {
    if ( !netInterface.isValid() ) {
        continue;
    }
​
    QNetworkInterface::InterfaceFlags flags = netInterface.flags();
​
    /* 网络接口处于活动状态 */
    if ( flags.testFlag ( QNetworkInterface::IsRunning ) && !flags.testFlag ( QNetworkInterface::IsLoopBack ) ) {
        qDebug() << "Device: " << netInterface.name(); /* 设备名 */
        /* 硬件地址 */
        qDebug() << "HardwareAddress: " << netInterface.hardwareAddress();
        /* 人类可读的名字 */
        qDebug() << "Human Readable Name: " << netInterface.humanReadableName();
    }
​
#if 0
    QList<QNetworkAddressEntry> entryList = netInterface.addressEntries();
​
    foreach ( QNetworkAddressEntry entry, entryList ) { /* 遍历每一个IP地址 */
        qDebug() << "IP Address: " << entry.ip().toString(); /* IP地址 */
        qDebug() << "Netmask: " << entry.netmask().toString(); /* 子网掩码 */
        qDebug() << "Broadcast: " << entry.broadcast().toString(); /* 广播地址 */
    }
​
#endif
}
```

执行结果：

``` cpp
Device: "{BE9972CD-860E-4E15-8CE2-3F25EF0A7A24}"
HardwareAddress: "94:DE:80:21:92:17"
Human Readable Name: "本地连接"

Device: "{29F85058-E757-4F60-BF7B-47F6227C8CBC}"
HardwareAddress: "00:50:56:C0:00:01"
Human Readable Name: "VMware Network Adapter VMnet1"
​
Device: "{A297491C-D43C-4F85-A674-88368F8D4FC1}"
HardwareAddress: "00:50:56:C0:00:08"
Human Readable Name: "VMware Network Adapter VMnet8"
​
Device: "{1AE5F6FC-478A-4EAB-B4D2-86201A6B2090}"
HardwareAddress: "0A:00:27:00:00:12"
Human Readable Name: "VirtualBox Host-Only Network"
```

通过`flags`函数，可以获取到当前网络接口的标识；利用`testFlag`进行过滤，就可以获取我们想要的内容(设备名、硬件地址、名字)。