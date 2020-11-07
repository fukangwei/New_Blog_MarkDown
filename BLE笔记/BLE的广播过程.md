---
title: BLE的广播过程
date: 2020-11-20 19:32:01
categories: BLE笔记
---
&emsp;&emsp;在`BLE`协议中，广播通信主要有`2`类使用场景：

- 单一方向的、无连接的数据通信，数据发送者在广播信道上广播数据，数据接收者扫描、接收数据。
- 连接的建立过程。

&emsp;&emsp;在`BLE`协议中，和广播通信相关的协议层次比较简单，主要包括：

``` cpp
GAP –> HCI -> LL
```

- `LL`(`Link Layer`)位于最底层，负责广播通信有关功能的定义和实现，包括物理通道的选择、相关的链路状态的定义、`PDU`的定义、设备过滤(`Device Filtering`)机制的实现等。
- `HCI`负责将`LL`提供的所有功能以`Command/Event`的形式抽象出来，供`Host`使用。
- `GAP`从应用程序的角度，抽象并封装`LL`提供的功能，以便让应用程序以比较傻瓜的方式进行广播通信。当然这不是必须的，也可以在没有`GAP`参与的情况下进行广播通信。

### Link Layer

&emsp;&emsp;从`LL`的角度看，在某一个时刻，参与广播通信的`BLE`设备可以处于如下三种状态的一种：

- `Advertising`：数据发送方，周期性的发送广播数据。
- `Scanning`：数据接收方，扫描、接收广播数据。
- `Initiating`：连接发起方，扫描带有`Connectable`标志的广播数据。一旦发现，则发起连接请求(都是由`Link Layer`自动完成，不需要`Host`软件参与)。

### PDU定义

&emsp;&emsp;根据应用场景的不同，处于不同状态的`BLE`设备可以发送不同类型的`PDU`(`Packet Data Unit`)。在广播通信中，传输的`PDU`有如下的格式(`Advertising Channel PDUs`)：

- `PDU Type`：`PDU`的类型。
- `RFU`：`Reserved for Future Use`的简称。
- `ChSel`：信道选择。
- `TxAdd`：发送数据的设备地址类型。如果该位是`0`，表示`Public Address`，`1`表示`Random Address`。
- `RxAdd`：接收数据的设备地址类型。如果该位是`0`，表示`Public Address`，`1`表示`Random Address`。
- `Length`：`PDU`的长度。

&emsp;&emsp;`PDU Type`的可选值如下表：

&emsp;&emsp;有关广播通信的`PDU`类型，总结如下：

1. 如果只需要定时传输一些简单的数据(例如某一个温度节点的温度信息)，后续不需要建立连接，则可以使用`ADV_NONCONN_IND`。广播者只需要周期性地广播该类型的`PDU`即可，接收者按照自己的策略扫描、接收，二者不需要任何额外的数据交互。
2. 如果除了广播数据之外，还有一些额外的数据需要传输(例如广播数据的长度限制、私密要求等)，可以使用`ADV_SCAN_IND`。广播者在周期性广播的同时，会监听`SCAN_REQ`请求。接收者在接收到广播数据之后，可以通过`SCAN_REQ PDU`进行回应，以请求更多的数据。
3. 如果后续需要建立点对点的连接，则可使用`ADV_IND`。广播者在周期性广播的同时，会监听`CONNECT_REQ`请求。接收者在接收到广播数据之后，可以通过`CONNECT_REQ PDU`请求建立连接。
4. 通过`ADV_IND/CONNECT_REQ`建立的连接，其花费的时间比较长。如果双方不关心广播数据，而只是想快速建立连接，恰好如果连接发起者又知道对方(广播者)的蓝牙地址(例如通过扫码的方式进行获取)，则可以通过`ADV_DIRECT_IND/CONNECT_REQ`的方式。

### Advertising

&emsp;&emsp;`BLE`可以使用`40`个`Physical Channel`中的`3`个作为广播通信的物理信道：

RF Channel | RF Center Frequency | Advertising Channel Index
-----------|---------------------|--------------------------
`0`        | `2402 MHz`          | `37`
`12`       | `2426 MHz`          | `38`
`39`       | `2480 MHz`          | `39`

&emsp;&emsp;与此同时，`Link Layer`允许`Host`在这三个物理信道中任意选取一个或者多个，用于广播。`Link Layer`将相同的广播数据在每一个被选中的`Channel`中发送一次。
&emsp;&emsp;`BLE`在广播的过程中，根据使用场景的不同，会在被使用的每一个物理信道上发送或接收多种类型的`PDU`。因此`BLE`协议提出了`Advertising Event`的概念：`Advertising Event`是在所有被使用的物理信道上，发送的`Advertising PDU`的组合。
&emsp;&emsp;通俗的语言解释如下：`BLE`设备处于`Advertising`状态的目的就是要广播数据。`BLE`设备最多可以在`3`个物理`Channel`上广播数据，同一个数据需要在多个`Channel`上依次广播。因此，依次在多个`Channel`上广播的过程，就叫做一个`Advertising Event`。
&emsp;&emsp;与此同时，有些广播(如可连接、可扫描)发送出去之后，允许接收端在对应的`Channel`上回应一些请求(如连接请求、扫描请求)。并且，广播者接收到扫描请求后，需要在同样的`Channel`上回应。这些过程也会包含在一个`Advertising Event`中。
&emsp;&emsp;根据应用场景的不同，BLE协议也规定了不同类型的`Advertising Event`，包括：

- `Connectable Undirected Event`
- `Connectable Directed Event`(包括`Low Duty Cycle`和`High Duty Cycle`)
- `Scannable Undirected Event`
- `Non-connectable Undirected Event`

&emsp;&emsp;不同的`Advertising Event`，所对应的`Advertising`参数(例如周期等)也不同。

### Scanning状态

&emsp;&emsp;`Scanning`状态用于扫描、接收广播数据，该状态的扫描行为是由`scanWindow`和`scanInterval`两个参数决定的：

1. `scanWindow`指示一次扫描的时间(可以理解为`RF`的`RX`打开时间)。
2. `scanInterval`指示两次扫描之间的间隔。

&emsp;&emsp;`BLE`协议规定，`scanWindow`和`scanInterval`最大不能超过`10.24s`，并且`scanWindow`不能大于`scanInterval`。
&emsp;&emsp;`Scanning`分为`Passive Scanning`和`Active Scanning`这`2`种模式，这`2`种`Scanning`把接收到的数据(包括`Advertiser`地址、`Advertiser`数据等)反馈给`Host`。

1. `Passive Scanning`：`BLE`设备只听不问，只接收`ADV_DIRECT_IND`、`ADV_IND`、`ADV_SCAN_IND`、`ADV_NONCONN_IND`等类型的`PDU`，并不发送`SCAN_REQ`。
2. `Active Scanning`：不仅认真听讲，还勤于发问(`SCAN_REQ`)，并接收后续的`SCAN_RSP`。

&emsp;&emsp;`Scan`和`ADV`是好基友，由于`ADV`总是先在`Primary Advertising Channel`上发送，所以`Scan`也是在`Primary Advertising Channel`上进行收包。

### Initiating状态

&emsp;&emsp;`Initiating`状态和`Scanning`状态类似，只不过它的关注点不一样：它不关心广播数据，只关心`ADV_DIRECT_IND`和`ADV_IND`两类消息，并在符合条件的时候，发出`CONNECT_REQ`，请求建立连接。
&emsp;&emsp;发起连接请求需要先准备几个东西：

1. 这么多`ADV`，`Initiating`要对哪个`ADV`发起请求呢？所以，在进入`Initiating`之前，需配置要连接的设备地址。
2. 怎样知道需要连接的设备地址？本端先进入`Scanning`状态，通过开窗扫描得知空中有哪些设备，以及它们的地址信息，这样就知道当前有些什么地址了。
3. 进入`Initiating`后，如何给想要连接的设备发起请求？我们知道了想要连接的设备地址，那么进入`Initiating`后，还需要继续开窗扫描。当又收到了感兴趣的那个设备地址，就发送连接请求，正式进入连接。