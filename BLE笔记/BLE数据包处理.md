---
title: BLE数据包处理
date: 2020-12-05 15:21:22
categories: BLE笔记
---
### 发送与接收数据包

&emsp;&emsp;对于`LL`层，它要发出`2`种类型的数据：<!--more-->

1. `Advertising`数据：广播数据。
2. `Data Channel`数据：两个设备建立连接之后发送的数据。

&emsp;&emsp;那么如何分辨是`Advertising`数据，还是连接之后的`Data Channel`数据呢？可以利用访问地址进行区分，分为`Random`地址或者固定值`0x8E89BED6`。对于广播数据，它的值永远是`0x8E89BED6`。当对端设备的`Link Layer`在收到一个数据包时，它首先去解析数据包的头部，如果发现等于`0x8E89BED6`，就知道这是一个`Advertising`数据；如果发现是一个`Random`地址，则表示这是一个`Data Channel`数据。
&emsp;&emsp;数据包头部的后面就是发给对端设备的数据了，这个数据的来源有两种可能：`LL`层或上层。那么如何区分这个数据是来自`LL`层，还是上层？在数据包中使用`LLID`来分辨数据来源。如果来自于`LL`层，那么`LLID = 11b`；如果数据来自于上层，那么`LLID = 10b`或`01b`。
&emsp;&emsp;如果数据包来自发送设备的上层，则其来源有`2`种可能：`L2CAP`层或者`ATT`层。它如何去区分是`L2CAP`层的数据，还是`ATT`层的数据？`L2CAP`层引入了一个`channel ID`，使用`channel ID`进行分辨：

- 当`channel ID = 0x0004`时，传输的是`ATT`层的数据。
- 当`channel ID = 0x0005`或`0x0006`时，传输的是`L2CAP`层的数据。

&emsp;&emsp;将上图中各层数据的格式组合起来，就得到了在空中传输的数据的格式了：

&emsp;&emsp;这些数据通过无线电传到对端的`LL`层，就开始了解包的过程。如果`LL`层能处理的话，它就进行处理，否则它会转交给上一层。
&emsp;&emsp;首先分辨`Access Address`，如果`Access Address`为`0x8E89BED6`，则是一个广播包，链路层是能够处理的，可以查看里面的数据；如果`Access Address`为`Random`地址，此时就需要区分这个数据包的目的层。
&emsp;&emsp;当`LLID = 11b`时，由`LL`层进行处理；当`LLID = 10b`或`01b`时，`LL`层无权处理，需要转交到`L2CAP`层进行处理。
&emsp;&emsp;`L2CAP`层如何进行处理呢？首先取出`Channel ID`，如果`Channel ID = 0x0005`或`0x0006`，它就需要处理后续的数据了；如果`Channel ID = 0x0004`，它无权处理，需要交给`ATT`层进行处理。

### L2CAP分片处理

&emsp;&emsp;应用程序通过`L2CAP`传送数据给对方，不应该限制数据包的大小，但是蓝牙`Controller`的内存是有限的，较大的数据包传进来，它的内存就不够用了。因此`L2CAP`传输数据时，如果数据包很大，应该把数据拆分成一个个小包，然后依次发给蓝牙`Controller`，再由蓝牙`Controller`发给对方。对方蓝牙`Controller`收到一个个小包后直接给`Host`，再由`Host`将小包组合起来。
&emsp;&emsp;注意，数据包的拆分和组装都是在`Host`这一层做的，它不会放到`Controller`中去做，毕竟`Controller`里的资源有限。对于不同的蓝牙协议栈，有的是在`L2CAP`拆分和组装，有的是在`HCI`层进行的拆分和组装。

&emsp;&emsp;1) 包很小，不同拆分，可以一次性传送出去
&emsp;&emsp;不需要拆分的数据包如下图所示：

&emsp;&emsp;`HCI`在数据包(黄色区域)的前面加上一个头部，头部中有个`Handle`，就代表了对端的设备；`PB = 00b`表示起始包；`Length`表示`L2CAP`整个数据包(即黄色区域部分，这里面有`Length`、`Channel ID`以及数据本身)的大小。`HCI`将数据构造好了之后，会通过串口或`USB`口把构造的新包传给`Controller`。
&emsp;&emsp;`LL`层在收到数据后，会将里面的来自`L2CAP`层的数据抽取出来。然后它也会构造一个头部：

- 根据`HCI`层传入的`Handle`得到`Access Address`。
- 根据`HCI`中的`PB`构造一个`Header`，`Header`中的`LLID`设为`10b`，也就是代表了起始包。
- 根据`HCI`中`Length`构造`Header`中的`Length`。

&emsp;&emsp;整个`LL`层构造的数据包就会通过无线信号发送给对端设备。

&emsp;&emsp;2) 包不可以一次性传送出去

&emsp;&emsp;如果数据包很大，无法直接发送出去，则需要分割成各个小包进行发送，如下图所示：