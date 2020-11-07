---
title: Host Controller Interface
date: 2020-11-22 11:56:51
categories: BLE笔记
---
&emsp;&emsp;`HCI`的全称是`Host Interface Controller`，它是`Host`和`Controller`之间进行通信的接口。<!--more-->
&emsp;&emsp;`BLE`目前支持的`HCI`有`4`种：`UART`、`USB`、`SDIO`和`3-Wire UART`。也就是说，当我们要使用`BLE`的时候，可以使用以上`4`种接口将其挂接到我们的`AP`或者`MCU`上，通过其总线来进行`BLE`芯片的控制。

&emsp;&emsp;这里分析`UART`串口的交互过程。使用`UART`往`Controller`发送字节流，首先将`UART`传输的`Bytes`流分为如下几类(第一个字节代表了这个`HCI`的类型)：

&emsp;&emsp;`HCI`定义了一套`Command-Event`机制，`Host`向`Controller`发送`HCI Command`，`Controller`向`Host`返回命令执行结果(使用`HCI Event`)。
&emsp;&emsp;如果`Host`与`Controller`之间要传输数据，那么可以使用`HCI ACL Data Packet`。在`LE`中，`HCI Synchronous Data Packet`没起到作用，暂时不用管它。

### 数据格式

&emsp;&emsp;当第一个字节`HCI Type`为`0x01`时，说明这个`HCI`是一个下发的命令类型。首先看一下`HCI Command`的格式：

- `OpCode`：由`OGF`(`OpCode Group Field`)和`OCF`(`OpCode Command Field`)共同组成`16 bit`的操作码。`OGF`代表该`HCI`命令所属的`group`，`OCF`代表特定的`HCI`命令。
- `Parameter Total Length`：指示该`Command`所有参数的长度。
- `Parameter_N`：`16 bits`的参数，由具体的`Command`决定。

&emsp;&emsp;`OGF`有如下类型：

&emsp;&emsp;与`Command`类似，当第一个字节`HCI Type`为`0x04`时，说明这个`HCI`是来自`Controller`的`Event`类型：

&emsp;&emsp;`ACL Data`的字节流只有在连接的状态才能够发送，当第一个字节`HCI Type`为`0x02`时，说明这个`HCI Packet`是`ACL Data`类型：

- `Handle`：由于`BLE`支持多连接，这些连接使用`Handle`进行区分。
- `PB Flag`：全称叫`Packet Boundary Flag`，它的描述如下(我们只关注`LE`的`Allowed`)，可以看出这个标志位用于控制`L2CAP`包：

1. 设置为`00b`时，代表`Host -> Contoller`的`L2CAP`的首包。
2. 设置为`01b`时，代表`Host -> Contoller`或者`Contoller -> Host`的`L2CAP`的续包(中间的)。
3. 设置为`10b`时，代表`Contoller -> Host`的`L2CAP`的首包。

- `BC Flag`：全称叫`Broadcast Flag`，它的描述如下：

&emsp;&emsp;`LE`没有`sniff mode`，所以都是点对点的数据传输，设置成为`00b`就好。

### HCI Command

&emsp;&emsp;简单介绍一下和广播通信有关的`HCI Command`，所有与`BLE`相关的`HCI Command`，其`OGF`都是`0x08`。

&emsp;&emsp;1. `Advertising`状态有关的`Command`

- `HCI_LE_Set_Advertising_Parameters`：设置广播参数，包括`Advertising Interval`、`Advertising Type`、本机的地址类型、对端设备的地址类型和地址、所使用的物理`Channel`的`map`、`Advertising Filter Policy`等。
- `HCI_LE_Set_Advertising_Data`：设置广播数据，`OCF`为`0x0008`。
- `HCI_LE_Set_Scan_Response_Data`：设置`Scan`请求时的应答数据，`OCF`为`0x0009`，格式和`HCI_LE_Set_Advertising_Data`一模一样。
- `HCI_LE_Set_Advertise_Enable`：控制`Advertising`的使能与否，`ICF`为`0x000a`，命令参数包括一个`8 bits`的`Advertising Enable`。

&emsp;&emsp;2. `Scanning`状态有关的`Command`

- `HCI_LE_Set_Scan_Parameters`：设置`Scan`参数，包括`Scan Type`、`Scan Interval`、`Scan Window`、本机的地址类型、`Scanning Filter Policy`等。
- `HCI_LE_Set_Scan_Enable`：`Scan`动作的开关，其数据格式和`HCI_LE_Set_Advertise_Enable`一致。

&emsp;&emsp;3. `Initiating`状态有关的`Command`

- `HCI_LE_Create_Connection`：建立连接，可指定`Scan Interval`、`Scan Window`、`Initiator Filter Policy`、`Peer Address Type`、`Peer Address`、`Own Address Type`等`Initiating`有关的参数，也可以指定连接相关的参数。
- `HCI_LE_Create_Connection_Cancel`：取消连接。

&emsp;&emsp;4. 白名单(`White List`)有关的`Command`

- `HCI_LE_Read_White_List_Size`：获取`BLE Controller`的白名单大小。
- `HCI_LE_Clear_White_List`：清空白名单。
- `HCI_LE_Add_Device_To_White_List`：将设备添加到白名单。
- `HCI_LE_Remove_Device_From_White_List`：将设备从白名单移除。

### HCI Event

&emsp;&emsp;当连接断开时，主机默认所有`Command`都已经执行完毕，将不再接收任何`Event`。`Controller`收到不同的`HCI Command`，可能返回以下类型`Event`：

- 执行完毕`Event`
- 状态信息`Event`

&emsp;&emsp;对于不涉及连接的`Command`，可以立即得到执行结果。`Command`执行完毕后，`Event`报告该`Command`执行成功或失败。
&emsp;&emsp;对于涉及连接的`Command`，无法立即得到执行结果。`Command`执行完毕后，先返回执行完毕`Event`，等到`Command`最终结果产生，再返回新的执行完毕`Event`。比如`LE Create Connection Command`，执行`Command`时先返回执行完毕`Event`，待两端设备建立连接，将返回连接完成`Event`。
&emsp;&emsp;对于部分读命令(比如`LE Read Advertising Channel Tx Power Command`)，执行完毕后将读取结果存放在状态信息`Event`中返回。
&emsp;&emsp;`HCI Event`包括`BLE`专有`Event`和通用`Event`，通用`Event`适用于经典蓝牙和`BLE`。`BLE`专有`Event`称为`元事件`(`LE Meta Event`)，共有`20`个，它们的`Event`代码均为`0x3E`。`Event`参数的第一个字节为`Subevent_code`，用于区分不同的元事件：

### 广播数据格式

&emsp;&emsp;`BLE`协议为广播数据和扫描应答数据定义了详细的格式：

&emsp;&emsp;首先，广播数据(或者扫描应答数据)由一个个`AD Structure`组成。每个`AD Structure`由`2`部分组成：`1 byte`的长度信息(`Data`的长度)和剩余的`Data`信息。`Data`信息又由`2`部分组成：`AD Type`(长度不定)指示该`AD Structure`的类型，以及具体的`AD Data`。
&emsp;&emsp;`BLE`协议根据实际的应用场景，定义了各种各样的`AD type`，以及相应的数据格式：

### HCI层跟LL层的关系

&emsp;&emsp;有些`HCI Command`只是用来设置本地`Controller`，不导致无线传输。
&emsp;&emsp;有些`HCI Command`会导致`LL`层发出各类广播包：

- `LE Set Scan Enable Command`在主动扫描时会导致`LL`层发出`SCAN_REQ`广播包。
- `LE Create Connection Command`会导致`LL`层发出`CONNECT_REQ`广播包。

&emsp;&emsp;有些`HCI Command`会导致LL层发出数据包，其中的`LLID = 11b`，表示是`LL Control PDU`：

- `LE Read Channel Map Command`会导致`LL`层发数据给对端设备，以便读取对端设备的信道图。

&emsp;&emsp;`ACL Data`是必定导致`LL`层发数据给对端设备的，这时的`LL`层数据有可能是起始包(`LLID = 10b`)，也可能是延续包(`LLID = 01b`)，也可能是空包(`LLID = 01b`)。

### HCI的Comand与Event实例

&emsp;&emsp;1) 扫描设备

- `Host`向`Controller`发出`LE Set Scan Parameters Command`。
- `Controller`向`Host`发送`Command Complete Event`。
- `Host`向`Controller`发出`LE Set Scan Enable Command`。
- `Controller`向`Host`发送`Command Complete Event`。
- 如果扫描参数为主动扫描，`LL`层发出`SCAN REQ`广播包。
- `Controller`接收到各类广播包，比如`ADV_IND`、`SCAN_RSP`。`LL`层解析后，会通过`HCI`上报`Event`。

&emsp;&emsp;2) 连接设备

- `Host`向`Controller`发出`LE Create Connection Command`。
- 不需要对端设备回应数据，发起方就认为连接已经建立。
- `Controller`向`Host`发送`Command Status Event`。
- `Controller`向`Host`发送`Command Complete Event`。

&emsp;&emsp;3) 写数据

- `Host`向`Controller`发出`ACL`数据：`Write Command, Handle...`。
- `Controller`的`LL`层构造数据包(`LLID = 10b`)，通过`PHY`发出无线信号。
- `Controller`向`Host`发送`Number of Completed Packets`事件。

&emsp;&emsp;4) 读数据

- `Host`向`Controller`发出`ACL`数据：`Read Request, Handle...`。
- `Controller`的`LL`层构造数据包(`LLID = 10b`)，通过`PHY`发出无线信号。
- `Controller`向`Host`发送`Number of Completed Packets`事件。
- 对端设备的`LL`层通过`PHY`返回数据(`LLID = 10b`)。
- `Controller`向`Host`发送`ACL`数据：`Read Response`。