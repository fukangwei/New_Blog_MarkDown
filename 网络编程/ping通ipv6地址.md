---
title: ping通ipv6地址
date: 2019-01-17 08:48:53
categories: 网络编程
---
### Windows

&emsp;&emsp;`ping ipv6主机名`的方法如下：<!--more-->

``` bash
ping -6 ipv6.google.com
```

&emsp;&emsp;`ping ipv6地址`的方法如下：

``` bash
ping IPv6Address[%ZoneID]
```

例如`ping 2001:4860:0:2001::68`。如果要`ping link-local地址`，则需要指定网络接口索引：

``` bash
ping fe80::260:97ff:fe02:6ea5%4
```

其中`%4`表示用索引为`4`的网络接口`ping`目标计算机。要查看网络接口索引，使用如下命令：

``` bash
netsh interface ipv6 show address
```

### Linux

&emsp;&emsp;`ping ipv6主机名`的方法如下：

``` bash
ping6 ipv6.google.com
```

&emsp;&emsp;`ping ipv6地址`的方法如下：

``` bash
ping6 IPv6Address[%InterfaceName]
```

如果要`ping link-local地址`，则需要指定网络接口名称：

``` bash
ping6 fe80::260:97ff:fe02:6ea5%eth0
```

其中`%eth0`表示用网络接口`eth0`来`ping`目标计算机。
&emsp;&emsp;使用`ping6`命令访问一个`IPv6`的地址时，经常出现`connect: Invalid argument`的错误。解决方法是在`IPv6`地址后加上`%设备名`，例如`ping6 fe80::172:16:212:4%eth0`。`Linux`使用`ifconfig`命令就可列出所有网络接口名称。

### 为什么Link-local地址后面要有百分号？

&emsp;&emsp;由于所有的`link-local`地址都有相同的前缀`FE80::/64`，并且每个网络接口都必须分配一个`link-local`地址，因而导致当发送数据包到一个`link-local`地址时，如果路由器使用普通的路由方法就无法决定选用哪个网络接口。因此引入了一种被叫做`zone index`的标识符，它提供额外的路由信息，这个标识符通常指网络接口，并且通过一个百分号被附加在`IPv6`地址后面。