---
title: Contiki的MAC和RDC层
date: 2019-02-04 14:32:30
tags:
---
&emsp;&emsp;在`Contiki`中，`MAC`层源码位于`core\net\mac`目录下。在低功耗网络中，无线收发设备必须经常休眠以节省电量。在`Contiki`中，负责这个工作的是`RDC`(`Radio Duty Cycling`)层。`Contiki`提供了一系列`RDC`机制，默认的就是`ContikiMAC`。

### 关于MAC驱动

&emsp;&emsp;`MAC`层处于`RDC`的上层，负责避免数据在传输过程中的冲突以及重传数据包。`Contiki`提供了两种`MAC`层，分别是`CSMA`(`Carrier Sense Multiple Access`，载波侦听多路访问)机制以及`NullMAC`机制(只负责对数据的转接而不对数据进行任何的操作)。`MAC`层接收来自`RDC`层的数据并通过`radio`层来转发数据包。假如`RDC`层或者是`radio`层检测到数据碰撞，`MAC`层就要重发这个数据包。如有数据碰撞，只有`CSMA`支持重发。

### 关于RDC驱动

&emsp;&emsp;`Contiki`有多种`RDC`驱动，最常用的就是`ContikiMAC`、`X-MAC`、`CX-MAC`、`LPP`以及`NullRDC`。默认的是`ContikiMAC`，它具有很好的功率效率，但有些更偏向于`IEEE 802.15.4`无线设备以及`CC2420`无线收发设备。`X-MAC`是一个较旧的机制，它不像`ContikiMAC`那样有高效率，但是有较严格的时序要求。`CX-MAC`(兼容的`X-MAC`)比`X-MAC`有更宽松的时序要求，因此能在更多的无线设备上工作。`LPP`(`Low-Power Probing`)是一个随接收器启动的`RDC`协议。`NullRDC`是一个空的`RDC`层，它从来不会关闭无线设备，因此可以用来测试或者与其它的`RDC`驱动比较使用。`RDC`驱动尽量保持无线设备休眠，并且定期检查无线传输介质。当进行检测时，无线设备接收数据包。信道检查频率以`Hz`为单位，指定每秒信道检查的次数，默认为`8Hz`。典型的信道检查频率为`2`、`4`、`8`、`16Hz`。
&emsp;&emsp;通常传输的数据包必须重复发送，直到接收器打开检测。这就增加了耗电量以及无线通信流量(`traffic`)，进而影响与其它节点的通信。一些`RDC`驱动允许阶段优化，即推迟选通(`strobe`)发送的数据包直到接收器将要唤醒。这需要两个节点之间良好的时间同步，如果以`1%`的时钟差，那么唤醒时间将贯穿整个发送阶段。窗口大小为`100`个周期，如果信道检查频率为`8Hz`，则窗口大小为`12`秒。当传送包的时间间隔为几秒的时候，这将让阶段优化失去作用，因为发送者必须在接收器唤醒之前提前准备好发送的数据。时钟漂移校正(`clock drift correction`)可能解决这个问题。这些`Contiki RDC`驱动被称作`contikimac_driver`、`xmac_driver`、`cxmac_driver`、`lpp_driver`和`nullrdc_driver`。另外，`SICSLoWMAC`是专门为`6LowPAN`的`MAC`层而设计的。

---

&emsp;&emsp;在`contiki_conf.h`，关于使用各种协议的定义如下：

``` cpp
#define NETSTACK_CONF_MAC    csma_driver
#define NETSTACK_CONF_RDC    contikimac_driver
#define NETSTACK_CONF_FRAMER framer_802154
#define NETSTACK_CONF_RADIO  stm32w_radio_drive
​
#if WITH_UIP6
    #define NETSTACK_CONF_NETWORK sicslowpan_driver
#else
    #define NETSTACK_CONF_NETWORK rime_driver
#endif
```

- `radio`：`STM32W`支持的射频部分的驱动，在`cpu/stm32w108/dev/stm32w_radio.c`文件中，相应的平台有相应的`radio`驱动。
- `Framer`：`IEEE 802.15.4`协议。
- `RDC`：`Radio`的管理机制在`core/net/mac`目录下，`sicslowmac.c`是一种机制，其他的可以通过设置编译到系统中。
- `MAC`：`MAC`层的管理机制，同样通过配置获得，由`csma_driver`可知这里选择的是`csma`机制，在`core/net/mac`目录下(`Contiki`用的`MAC`应该符合`802.15.4`协议，因为它是基于`802.15.4`的`MAC`来开发的网络层和应用层)。
- 适配层：`6LowPAN`适配层，对`MAC`层的数据进行压缩报头等数据分析与重组。在`core/net/sicslowpan.c`中，由`input`函数进入此函数。
- `network`：对数据进行处理，由`core/net/tcpip.c`中的`tcpip_input`、`packet_input`和`uip_input`函数完成，最后将数据交给`uIP`协议栈，由`UIP_APPCALL`函数将数据包分发给不同的应用程序。
- `App`：应用程序。

&emsp;&emsp;从这个过程看，`radio`层中的驱动是与硬件平台相关的，只能对于相应的硬件平台选择适当的`driver`，而其他各层却不是与硬件层紧密相连的。但是一些实现需要硬件的支持，所以有些虽然不与硬件之间相关，但是由于硬件本身不支持，所以无法实现一些算法。尤其是在`RDC`层，因为这个涉及无线射频的功耗，也是`Contiki`作为优秀的网络系统的原因。从`RDC`和`MAC`的文件在同一个文件夹下可知，它们之间应该是有紧密的联系；适配层和`Network`在同一个文件夹下可知，它们应该是有紧密的联系。