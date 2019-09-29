---
title: 配置SLIP链路
categories: Linux应用笔记
abbrlink: e88b1030
date: 2019-02-02 14:43:27
---
&emsp;&emsp;`SLIP`即`Serial Line IP`，它是一个数据链路层协议，用于在串行线路上传输`IP`数据报。本文讲述如何在两台用串口线(`RS232`)连接的`Linux`机器之间配置`SLIP`链路。设两台机器为`A`和`B`，首先将两台机器用串口线连接好，然后在`A`机器上依次运行如下指令：

``` bash
slattach /dev/ttyS0 -p slip -s 9600 -m -d &
ifconfig sl0 192.168.1.1 pointopoint 192.168.1.2 up
route add default gw 192.168.1.2
```

其中，`/dev/ttyS0`是第`1`个串口设备，如果有多个串口，则依次是`/dev/ttyS1`、`/dev/ttyS2`。
&emsp;&emsp;`slattach`的`-p`选项指定要使用的数据链路层协议，可以是`slip`、`cslip`、`ppp`等；`-s`指定传输速率，可以是`9600`、`115200`等；`-m`告诉串口设备不要工作在`RAW data`模式，而是要工作在协议驱动模式；`-d`输出调试信息。`ifconfig`可以配置串行接口的`ip`信息，`sl0`代表第一个串行接口，如果有更多，依次是`sl1`、`sl2`等。`route`将对方`ip`添加为默认网关。
&emsp;&emsp;然后在`B`机器上依次运行以下指令：

``` bash
slattach /dev/ttyS0 -p slip -s 9600 -m -d &
ifconfig sl0 192.168.1.2 pointopoint 192.168.1.1 up
route add default gw 192.168.1.1
```

指令和`A`一样，要注意的是`ip`地址要设置对。两边所用的协议、传输速率也要一样。
&emsp;&emsp;如果没有出错，连接就建立成功了，可以用在`A`或`B`上运行`ping 对方地址`来测试连接是否畅通。建立好`SLIP`链路后，就可以使用互联网套接字编程来进行二者之间的通信，而不必关心底层是串行线路还是以太网线。如果不配置此链路，则串口设备工作在`RAW data`模式，收、发的数据都是原始数据，不走协议栈，不进行`IP`等封装。
&emsp;&emsp;**补充说明**：我使用的是`USB`转串口，设备文件是`/dev/ttyUSB0`，第一条命令应该是`slattach -l /dev/ttyUSB0 -p slip -s 9600 -m -d &`。