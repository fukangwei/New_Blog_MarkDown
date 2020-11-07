---
title: BLE连接
date: 2020-11-24 20:40:15
categories: BLE笔记
---
&emsp;&emsp;在经典蓝牙中，保持连接(`Connection`)是一个相当消耗资源(`Power`和带宽)的过程。特别是当没有数据传输的时候，所消耗的资源完全被浪费了。因而，对很多蓝牙设备来说(特别是功耗敏感的设备)，希望在没有数据需要传输时能够断开连接。但是，由于跳频(`Hopping`)以及物理通道(`Physical Channel`)划分的缘故，经典蓝牙连接建立的速度要好几秒。对那些突发的数据传输来说，几秒钟的连接延迟，简直是灾难。<!--more-->
&emsp;&emsp;因此，蓝牙`SIG`制订`BLE`规范时，充分考虑了这方面的需求，极大地简化了连接的建立过程，使连接速度可以达到毫秒级(最快为`3.75ms`)。与此同时，为了节省功耗，也调整了跳频的策略。至此，相比于广播通信，`BLE`面向连接的通信几乎没有额外的代价。

### 建立连接

&emsp;&emsp;从字面上理解，建立了连接就是指二者之间建立了一条专用的通道，它们可以随时随地的通信。当然，在蓝牙这种资源有限的通信系统中，通道无法独占，退而求其次，分时也是可以的。因此，`BLE`建立连接的定义为：在约定的时间段内，双方都到一个指定的物理`Channel`上通信。其中，约定好的时间段是时分的概念；而到指定的物理`Channel`上是跳频的概念。

### Data Channel PDU

&emsp;&emsp;和广播通信不同，面向连接的通信使用特定的`PDU`，称作`Data Channel PDU`：

- `LLID`：区分`PDU`的类型。`Data Channel`传输的`PDU`有`2`类：一类是数据，称作`LL Data PDU`；另一类是控制信息，称作`LL Control PDU`。
- `NESN`(`Next Expected Sequence Number`)和`SN`(`Sequence Number`)：用于数据传输过程中的应答(`Acknowledgement`)和流控(`Flow Control`)。
- `MD`：`More Data`的简称，决定了后面是否还有数据要发送。有了这个`MD`，对端设备才会开窗继续来接收数据。
- `RFU`：`Reserved for Future Use`的简称。
- `Length`：有效数据的长度(`Payload + MIC`)，只有`8bits`，因此`Link Layer`所能传输的最大数据是`255bytes`(有`MIC`的话是`251bytes`)。如果`L2CAP`需要传输更多的数据，需要分包之后传输。
- `Payload`：装载`L2CAP`层数据包或链路层控制命令。对于数据包，最大`Payload`长度为`251`字节。
- `MIC`：完整性检测(`Message Integrity Check`)，用于确认加密的`Payload`是否完整有效。当`Payload`不加密或者`Payload`长度为`0`时，`MIC`字段不存在。

### LL Data PDU

&emsp;&emsp;`LL Data PDU`有`2`种：

- `LLID = 01b`：`Continuation fragment of an L2CAP message, or an Empty PDU`。这种类型的`PDU`，要么是一个未传输完成`L2CAP message`(长度超过`255`，被拆包，此时不是第一个)，要么是一个空包(`Header`中的`Length`为`0`)。
- `LLID = 10b`：`Start of an L2CAP message or a complete L2CAP message with no fragmentation`。这种类型的`PDU`，要么是`L2CAP message`的第一个包，要么是不需要拆包的完整的`L2CAP message`。无论哪种情况，`Header`中的`Length`均不能为`0`。

### LL Control PDU

&emsp;&emsp;`LLID = 11b`时，表示这个数据包是用于控制、管理`LL`连接的`LL Control PDU`：

&emsp;&emsp;其中`Opcode`用于指示`Packet`的类型：

- `LL_CONNECTION_UPDATE_IND`：连接参数的更新。
- `LL_CHANNEL_MAP_IND`：`Channel Map`的更新。
- `LL_TERMINATE_IND`：连接即将被关闭的通知(可以通知被关闭的原因，即`ErrorCode`)。
- `LL_VERSION_IND`：

1. `VersNr`：蓝牙`Link Layer`规范的版本。
2. `CompId`：蓝牙`Controller`制造商的公司标识符。
3. `SubVersNr`：蓝牙`Controller`每个实现的唯一值。

- `LL_CONNECTION_PARAM_REQ`：

1. `Interval_Min`：指示`connInterval`的最小值，`connInterval = Interval_Min * 1.25ms`。
2. `Interval_Max`：指示`connInterval`的最大值，`connInterval = Interval_Max * 1.25ms`。
3. `Latency`：指示`connSlaveLatency`的值，`connSlaveLatency = Latency`，`Latency`以`number of connection events`为单位。
4. `Timeout`：指示`connSupervisionTimeout`的值，`connSupervisionTimeout = Timeout * 10ms`。
5. `PreferredPeriodicity`：指示`connInterval`最好是其倍数的值。`PreferredPeriodicity`以`1.25ms`为单位。例如，如果`PreferredPeriodicity`设置为`100`，则表示`connInterval`最好是`125ms`的任意倍数。零值表示无效。`PreferredPeriodicity`应小于或等于`Interval_Max`。

- `LL_REJECT_EXT_IND`：

1. `RejectOpcode`：包含被拒绝的`LL Control PDU`的`Opcode`字段值。
2. `ErrorCode`：应当包含`LL Control PDU`被拒绝的原因。

- `LL_LENGTH_REQ`和`LL_LENGTH_RSP`：

1. `MaxRxOctets`：`Sender`的`connMaxRxOctets`值。
2. `MaxRxTime`：`Sender`的`connMaxRxTime`值。
3. `MaxTxOctets`：`Sender`的`connMaxTxOctets`值。
4. `MaxTxTime`：`Sender`的`connMaxTxTime`值。

- `LL_PHY_REQ`和`LL_PHY_RSP`：

1. `TX_PHYS`：`Sender`喜欢使用的`transmitter PHYs`。
2. `RX_PHYS`：`Sender`喜欢使用的`receiver PHYs`。

- `LL_PHY_UPDATE_IND`：

1. `M_TO_S_PHY`：指示`Master`发送到`Slave`的包所使用的`PHY`。
2. `S_TO_M_PHY`：指示`Slave`发送到`Master`的包所使用的`PHY`。

### 连接的建立

&emsp;&emsp;对`BLE`来说，连接建立的过程很简单，包括如下步骤：

1. 处于`Connectable`状态设备(`Advertiser`)按照一定的周期广播`ADV_IND`或者`ADV_DIRECT_IND`包。
2. 主动连接的设备(`Initiator`)在收到广播包之后，会回应一个`CONNECT_REQ`请求，该请求携带了可决定后续通信时序的参数，例如双方在哪一个时间点、哪一个`Physical Channel`收发数据。
3. `Initiator`在发出`CONNECT_REQ`数据包之后，自动转变为`Connection`状态，成为`Master`角色(注意，这是自动的，不需要等待另一方的回应)。同样，`Advertiser`在收到`CONNECT_REQ`请求之后，也自动转变为`Connection`状态，成为`Slave`。
4. 此后，双方按照`CONNECT_REQ`参数所给出的约定，定时到切换到某一个`Physical Channel`上，按照`Master -> Slave`然后`Slave -> Master`的顺序收发数据，直至连接断开。

### Connectable and Scannable Undirected Event

&emsp;&emsp;这种可连接、可扫描、不定向的广播只有一种格式，就是`ADV_IND`。它在空中发送，有时序的要求，通道`37`、`38`和`39`发送完毕才算一组`ADV`完成，也就是完成了一个`Event`。每两个包之间的间隔是`10ms`以内：

&emsp;&emsp;既然是`Scannable`的，那么它就能够与`Scanner`进行交互。交互的过程是，当`Scanner`在一样的信道上扫描到了这个包(`Scanner`会在`37`上开窗扫描，一会儿又在`38`上开窗扫描，一会又在`39`上开窗扫描)，那么`Scanner`就会发送`SCAN_REQ`；如果`Advertising`端收到了这个`SCAN_REQ`，就会回复`SCAN_RSP`。需要注意参数`T_IFS`，它的数值是`150 us`。

&emsp;&emsp;当对端设备处于`Initiating`状态时，在同样的信道上扫描到了你的`ADV_IND`，则立即发送`CONNECT_IND`，并且进入连接状态。注意，此刻的连接没有握手：

&emsp;&emsp;那么问题来了，万一`Advertiser`那边运气不好，没有收到`CONNECT_IND`怎么办？`Advertiser`这边就进入不了连接状态的`Slave`。`Initiator`以为开始连接了，便兴冲冲地做起了`Master`。在连接建立初期，如果在`6`个`Interval`中没有进行包交互，那么视为连接建立失败，`Master`也只能垂头丧气(一旦进入连接状态，就视为建立了连接)。

### Connectable Directed Event

&emsp;&emsp;这种是可连接、不可扫描、定向的`Advertising`。从名字可以得知，这种类型的`ADV`完全是冲着指定的设备发起广播，并求成为`Slave`，让对方连接的。这种广播又分为`2`类：

- `Low Duty Cycle Connectable Directed Advertising`
- `High Duty Cycle Connectable Directed Advertising`

&emsp;&emsp;`Low Duty Cycle Connectable Directed Advertising`：`Low Duty`是相对于`High Duty`的，其实就是正常的发送定向广播而已：

&emsp;&emsp;由于它是不可扫描的，所以不会引起对方发`SCAN_REQ`，只会和`CONNECT_IND`交互：

&emsp;&emsp;`High Duty Cycle Connectable Directed Advertising`：`High Duty`用于快速建立连接，使用的方法就是缩短数据包地发送时间。对于这种类型，同信道的两个包之间的间隔必须小于等于`3.75ms`，并且在`1.28s`以内必须退出这种模式：

### Scannable Undirected Event

&emsp;&emsp;这种是不连接、可扫描、不定向的`Advertising`，其作用只是为了让对方进行扫描的交互。
&emsp;&emsp;`BLE 5.0`之前使用`ADV_SCAN_IND`数据包来进行这种`Advertising`。它只会在`Primary`通道上发送，仅仅是为了让`Scanner`进行交互，完全不去理会`CONNECT_IND`的请求。

&emsp;&emsp;当`Scanner`在同一个通道上扫描到了这种包，那么会发送`SCAN_REQ`，本端则回复`SCAN_RSP`，完成交互：

### 连接建立后的通信过程

&emsp;&emsp;当`Master`发出或者`Slave`收到`CONNECT_REQ`后，它们就自动进入连接状态，那双方在收发数据的时间窗口怎么确定呢？可参考下面的图片：

&emsp;&emsp;从`Master`的视角看，当它发出`CONNECT_REQ`后，会在`1.25ms + transmitWindowOffset`到`1.25ms + transmitWindowOffset + transmitWindowSize`之间，发送第一个`Packet`(`M -> S`)。同理，`Slave`在收到`CONNECT_REQ`之后，也会在相应的时间区间去接收`Packet`(`M -> S`)。
&emsp;&emsp;`transmitWindowOffset`可以控制这个`LL Connection`使用哪一段时间进行通信，从而保证了一个`Master`和多个`Slave`之间的正常连接，使其通信互不影响(时分)。`transmitWindowOffset`的取值范围是`0ms`到`connInterval`。
&emsp;&emsp;从`Master`发出`CONNECT_REQ`，到`Slave`接收到`CONNECT_REQ`，之间是有一定的时间延迟的，因此需要一定的时间窗口`transmitWindowSize`，才能保证第一个`Packet`被正确地发送并被接收。`transmitWindowSize`必须是`1.25ms`的倍数，最小值是`1.25ms`，最大值是`connInterval - 1.25ms`，但不能超过`10ms`。
&emsp;&emsp;正常情况下，所有`M -> S`数据包的发送不能超过`transmitWindowSize`，以便留出`S -> M`的时间，但是第一个`Packet`例外。
&emsp;&emsp;`Master`发出第一个`Packet`之后，将以此为起始点(称作`Anchor Point`)，以`connInterval`为周期，接着发送后续的`Packet`(`M -> S`)，以及接收`Slave`的`Packet`(`S -> M`)。
&emsp;&emsp;这样以`connInterval`为周期的发送(`M -> S`)、接收(`S -> M`)组合(可能有多个)，称作`Connection Event`。因此，`BLE`面向连接的通信基础就是`Connection Event`。
&emsp;&emsp;`connInterval`的大小决定了数据传输的周期。对一个连接来说，每个周期只能有一次的收发，因此`connInterval`的选择直接决定了数据传输的速度。`BLE`协议规定，`connInterval`必须是`1.25ms`的倍数，范围是`7.5ms`至`4s`。
&emsp;&emsp;`Slave`如果没有收到第一个`Packet`(`M -> S`)，则会以`1.25ms + transmitWindowOffset`为起点，等待`connInterval`之后，再次尝试接收，直到接收到为止。`Slave`接收到`Packet`之后，则以收到该`Packet`的时间点为`Anchor Point`，以`connInterval`为周期，接着接收后续的`Packet`(`M -> S`)，以及发送`Packet`给`Master`(`S -> M`)。
&emsp;&emsp;注意，`BLE`的面向连接通信使用了跳频技术，即每次`Connection Event`都会使用不同`Physical Channel`收发数据。

### Connection Event

&emsp;&emsp;`Connection Event`(简称`CE`)代表着一个连接态的`Master/Slave`的交互集合。两个`CE`的起始时间之间的时间差叫做`Connection Interval`(简称`CI`)。

&emsp;&emsp;可以看到，在一个`Event`内收发数据包，一定是`Master`先发起，`Slave`再回复，并且是交替进行。如果有数据，那么包内就是`Raw Data`；如果没有数据，双方也要在一个`Event`内发送空包交互。当建立连接后，双方都会记录一个数值`Event Counter`，这个值代表了当前是第几个`Connection Event`。
&emsp;&emsp;连接建立成功之后，就可以开始传送数据。`BLE`是周期性业务，所以每次数据交互只能在每个`Connection Event`中进行。那么一个`Connection Event`有多长呢？这个就涉及到一个概念，叫`MD`(`More Data`)。
&emsp;&emsp;在数据包的`Header`中，有一个叫做`MD`的标志位，它代表了当前的这个包后面还有没有跟进其他的非空数据包。比如，`Master`想发`40`个`Bytes`的数据(默认情况下，`27 Bytes`为一个`Date Length`)，那么就要分为两个包发送，第一次发送`27 Bytes`，第二次发送`13 Bytes`。因此，第一个包中的`MD`字段就要被设置成为`1`，而第二个数据包的`MD`字段就被设置成为`0`。
&emsp;&emsp;那么，这个`MD`是如何与`CE`关联上的呢？例如，如果双方都没有要传输的数据，那么`MD`都为`0`，则这次的`CE`就提前结束，`CE`就显得比较小。

&emsp;&emsp;如果`Master`有`40 Bytes`的数据传送，那么`Slave`也要陪着`Master`玩，那么这时的`CE`就会较大：

&emsp;&emsp;当然，只允许`Master`传数据那是不合理的，`Slave`也可以发送据。当`Slave`发送数据，而`Master`没数据时，`Slave`的`MD`就会被设置成为`1`，`Master`也只有陪玩：

&emsp;&emsp;当然，`Master`和`Slave`可以同时发数据，`MD`都被设置成为`1`：

&emsp;&emsp;可以看出，`MD`会影响到`CE`的长度，同时可看出来，`CE`最长不能超过`CI`的长度。有没有办法指定`CE`呢？当然可以，`HCI Command`可以指定`CE MIN Length`和`CE MAX Length`。如果一个`CE`装不了那么多数据，那么数据顺延到下一个`CI`去发就好，`MD`照样置上。

### 关闭连接事件

&emsp;&emsp;关闭连接事件并非断开连接，仅表示当次数据传输事件完毕。
&emsp;&emsp;当连接事件中仅有一个`PDU`需要发送，则发送完毕后即可关闭连接事件。
&emsp;&emsp;如果连接事件中有多个`PDU`需要发送，那么将在`PDU`中设置`MD`字段。在一个`PDU`发送完毕后持续发送，直到全部`PDU`发送完毕，再关闭连接事件。
&emsp;&emsp;每次`Master`发送一个`PDU`，`Slave`都需要返回一个响应。假如`Slave`没有返回响应，则`Master`中断发送，关闭连接事件。如果`PDU`没有发送完毕，`Slave`没有收到`Master`发送的数据包，则`Slave`关闭连接事件。如果`PDU`的`CRC`校验失败，则关闭连接事件。

### 数据传输速率

&emsp;&emsp;由上面的通信过程可知，`BLE`面向连接的通信速率是由`connInterval`，以及每个`Connection Event`中所传输的数据量决定的。
&emsp;&emsp;`LL Data PDU`的有效负荷不能超过`255bytes`。考虑到一次传输的效率、错误处理等因素，实际上`Link Layer`不会使用这么大的`Packet`。并且，为了提高传输速度，一般会在一个`Connection Event`中传输多个`Packet`。以`IOS`为例，它可能会在一个`Connection Event`中传输`6`个`Packet`，每个`Packet`的长度是`20bytes`。

### 连接的控制与管理

&emsp;&emsp;连接建立之后，`Master`或`Slave`可以借助`Link Layer Control Protocol`(`LLCP`)，通过`LL Control PDU`对连接进行管理控制：

- `Connection Update Procedure`：连接参数(包括`connInterval`、`connSlaveLatency`和`connSupervisionTimeout`)更新的通知，只能由`Master`发起。
- `Channel Map Update Procedure`：更新`Channel Map`，只能由`Master`发起。
- `Encryption Procedure`：对连接进行加密，可由`Master`或`Slave`发起。
- `Termination Procedure`：断开连接。
- `Connection Parameters Request Procedure`：请求更新连接参数(`connInterval`、`connSlaveLatency`和`connSupervisionTimeout`)，`Slave`或`Master`都可以发起。和`Connection Update Procedure`不同是，这是一个协商的过程，不一定能够成功。
- `LE Ping Procedure`：类似于网络协议中`Ping`操作。

### 连接超时以及断开

&emsp;&emsp;`BLE`连接断开的原因有`2`种：

- 预期内的、主动断开，此时会走`Termination Procedure`过程。
- 一些非预期的原因导致的超时断开，例如距离超出、遭受严重的干扰、突然断电等。

&emsp;&emsp;对于第二种连接断开的异常情况，需要一些`Timeout`机制进行检测：

1. `Master`和`Slave`的`Link Layer`都会启动一个名称为`TLLconnSupervision`的`Timer`，每接收到一个有效的数据包时，该`Timer`都会重置。
2. 连接建立的过程中，如果`TLLconnSupervision`超过`6 * connInterval`没有接收到第一个数据包，则认为连接建立失败。
3. 在连接成功之后，如果`TLLconnSupervision`超过`connSupervisionTimeout`，则说明`Link Loss`，此时执行超时断开。`connSupervisionTimeout`是一个可配置的参数，范围是`100ms`至`32s`，并且不能大于`(1 + connSlaveLatency) * connInterval * 2`。
4. `BLE`协议允许`Slave`忽略掉`connSlaveLatency`个`Connection Event`，在被忽略的这段时间内，`Slave`不需要收发数据包，也不会增加`TLLconnSupervision`。`connSlaveLatency`是一个整数，有效范围应该在`0`到`(connSupervisionTimeout / (connInterval * 2)) - 1`之间，并且不能大于`500`。

&emsp;&emsp;注意，`connSlaveLatency`是一个非常有用的参数，它允许`Slave`在数据通信不频繁的时候，忽略掉一些`Connection Event`，进而可以睡得更久，更加省电。

### 跳频策略

&emsp;&emsp;`BLE`的跳频策略(`Hopping`)是非常简单的，即每一个`Connection Event`更换一次`Physical Channel`。当然，`Master`和`Slave`需要按照相同的约定更换，不然就无法通信。这个约定如下：

&emsp;&emsp;首先，使用一个`Basic`的算法，利用`lastUnmappedChannel`和`hopIncrement`计算出`unmappedChannel`。确定`unmappedChannel`的算法为`unmappedChannel = (lastUnmappedChannel + hopIncrement) mod 37`，本质上就是隔`hopIncrement`取一次`Channel`。
&emsp;&emsp;`lastUnmappedChannel`在连接建立之初的值是`0`，每一次`Connection Event`计算出新的`unmappedChannel`之后，会更新`lastUnmappedChannel`。
&emsp;&emsp;`hopIncrement`是由`Master`在连接建立时随机指定的，范围是`5`到`16`。
&emsp;&emsp;计算出`unmappedChannel`之后，查找当前的`Channel Map`，检查`unmappedChannel`所代表的`Channel`是否为`used channel`。如果是，恭喜你找到了。`Channel map`也是由`Master`在连接建立或者后来的`Channel Map Update`时指定的。
&emsp;&emsp;如果不是，将所有的`used Channel`以升序的方式建立一个表，表的长度是`numUsedChannels`。用`unmappedChannel`和`numUsedChannels`做模运算，得到一个`index`，按照该`index`从表中取出对应的`Channel`即可。

### 应答和流控

&emsp;&emsp;在`LL Data PDU`的`Header`中，有`NESN`(`Next Expected Sequence Number`)和`SN`(`Sequence Number`)两个标记，使用它们可以轻松地在`Link Layer`实现应答、重传、流控等机制。
&emsp;&emsp;为了实现这些功能，`Link Layer`会为每个连接创建两个变量：`transmitSeqNum`和`nextExpectedSeqNum`(为了和`Packet`中的`SN/NESN bit`进行区分，我们将它们简称为`sn`和`nesn`)，并在连接建立时，都被初始化为`0`。

- `sn`用于标识本地设备(`Link Layer`)发送出去的`Packet`。
- `nesn`用于标识对端设备(`Link Layer`)应答本地设备发送的`Packet`，或者请求本地设备重发`Packet`。

&emsp;&emsp;`Link Layer`在收发`Packet`时，会遵循如下的原则：

&emsp;&emsp;1) 无论是`Master`还是`Slave`，发送`Packet`时，都会将当前的`sn`和`nesn`复制到`Packet`的`SN`和`NESN`中。
&emsp;&emsp;2) 无论是`Master`还是`Slave`，当接收到一个`Packet`时，会将该`Packet`的`NESN`和本地的`sn`比较：如果相同，说明该`Packet`是对端设备发来的`NAK Packet`(请求重发)，则需要将旧的`Packet`重新发送出去；如果不同，说明是对端设备发来的`ACK Packet`(数据被正确接收)，则需要将本地的`sn`加`1`，接着发送新的`Packet`。

&emsp;&emsp;补充说明`1`：以上过程可以参考下图。本地的`sn`代表本地设备已经发送出去的`Packet`，而`Packet`中的`NESN`代表对端设备期望本地设备发送的`Packet`。如果二者相同，说明对方期望下次发送的`Packet`和我们已经发送的`Packet`相同，因此是`NAK`信号，要求重发。如果二者不同，说明对方设备期望发送一个新的`Packet`，也说明我们上次发送的`Packet`已经成功接收，因此可以将本地的`sn`加`1`。

&emsp;&emsp;3) 无论是`Master`还是`Slave`，当接收到一个`Packet`时，会将该`Packet`的`SN`和本地的`nesn`比较：如果相同，则说明是一个新的`Packet`，接收即可，同时将本地的`nesn`加`1`；如果不同，则说明是一个旧的`Packet`，什么都不用做。
&emsp;&emsp;补充说明`2`：以上过程可以参考下图。`Packet`中的`SN`代表对端设备正在发送的`Packet`；而本地设备的`nesn`代表本地设备期望对端设备发送的`Packet`。如果二者相同，则说明是一个期望的`Packet`(新的)，并将其收下，然后将期望值加`1`(`nesn`加`1`)。如果二者不同，说明不是本地设备期望的`Packet`，什么都不用做。

&emsp;&emsp;注意，上面的步骤`2)`和`3)`是相互独立的。
&emsp;&emsp;当一个设备无法接收新的`Packet`时(例如`RX buffer`已满)，它可以采取不增加`nesn`的方式，发送`NAK Packet`。对端设备收到该类型的`Packet`之后，会发送旧的`Packet`。该设备收到旧的`Packet`时，不会做任何处理。这就是`Link Layer`的流控机制(`Flow Control`)。