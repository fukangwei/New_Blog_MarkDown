---
title: Attribute Protocol
date: 2020-11-08 12:01:51
categories: BLE笔记
---
### ATT的功能

&emsp;&emsp;在`BLE`协议栈中：<!--more-->

- `Physical Layer`负责提供一系列的`Physical Channel`。
- 基于这些`Physical Channel`，`Link Layer`可在两个设备之间建立用于点对点通信的`Logical Channel`。
- `L2CAP`则将这个`Logical Channel`划分为一个个的`L2CAP Channel`，以便提供应用程序级别的通道复用。

&emsp;&emsp;至此，基本协议栈已经构建完毕，应用程序已经可以基于`L2CAP`欢快地运行起来了。
&emsp;&emsp;说到应用程序，就不得不说`BLE`的初衷 -- **物联网**。物联网中最重要、最广泛的一类应用是信息采集，这些信息往往都很简单，例如温度、湿度、速度等。采集的过程也很简单，节点设备定时地向中心设备汇报信息数据，或者中心设备在需要的时候主动进行查询。
&emsp;&emsp;基于信息采集的需求，`BLE`抽象出一个协议：`Attribute Protocol`，该协议将这些信息以属性(`Attribute`)的形式抽象出来，并提供一些方法，供远端设备(`Remote Device`)读取、修改这些属性的值(`Attribute Value`)。
&emsp;&emsp;`Attribute Protocol`的主要思路包括：

- 基于`L2CAP`，使用固定的`Channel ID`(`0x0004`)。
- 采用`Client-Server`的形式：提供信息(`Attribute`)的一方称作`ATT Server`(一般是那些传感器节点)，访问信息的一方称作`ATT Client`。
&emsp;&emsp;一个`Attribute`由`Attribute Type`、`Attribute Handle`和`Attribute Value`组成：

- `Attribute Type`用于标示`Attribute`的类型，类似于温度、湿度等人类可识别的术语。不过与人类术语不同的是，`Attribute Type`使用`UUID`(`Universally Unique identifier`，具有`16 bit`、`32 bit`或者`128 bit`的数值)区分。
- `Attribute Handle`是一个`16 bit`的数值，用作唯一识别`ATT Server`上的所有`Attribute`。`Attribute Handle`的存在有如下意义：

&emsp;&emsp;1) 一个`Server`上可能存在多个相同`Type`的`Attribute`，因此`Client`有区分这些`Attribute`的需要。
&emsp;&emsp;2) 同一类型的多个`Attribute`可以组成一个`Group`，`Client`可以通过这个`Group`中的起始`Handle`访问所有的`Attributes`。

- `Attribute Value`代表`Attribute`的值，可以是任何固定长度或者可变长度的`Octet Array`(字节类型的数组)。`Attribute Value`的长度信息不包含在`PDU`中，所以需要从`PDU`的长度间接推算出`Attribute Value`的长度信息。`Attribute Value`通常在一个`PDU`中发送，如果`Attribute Value`太长，也可以分成多个`PDU`进行发送。

&emsp;&emsp;`Attribute`可以定义一些权限(`Permissions`)，以便`Server`控制`Client`的访问行为：

- 访问有关的权限(`Access Permissions`)：`Readable`、`Writable`以及`Readable and Writable`。
- 加密有关的权限(`Encryption Permissions`)：`Encryption Required`和`No encryption Required`。
- 认证有关的权限(`Authentication Permissions`)：`Authentication Required`和`No Authentication Required`。
- 授权有关的权限(`Authorization Permissions`)：`Authorization Required`和`No Authorization Required`。

&emsp;&emsp;根据所定义的`Attribute PDU`的不同，`Client`可以对`Server`有多种访问方式：

- `Find Information`：获取`Attribute Type`和`Attribute Handle`的对应关系。
- `Reading Attributes`：有`Read by Type`、`Read by Handle`、`Read by Blob`(只读取部分信息)、`Read Multiple`(读取多个`Handle`的`Value`)等方式。
- `Writing Attributes`：包括需要应答的`Writing`、不需要应答的`Writing`等。

### 关于UUID

&emsp;&emsp;`UUID`是指从时间尺度和空间尺度都具有唯一性的一串`128 bit`的数字，该数字串在全球范围内不会重复，并且在未来也不会出现重复。
&emsp;&emsp;蓝牙协议设定了一个蓝牙基础`UUID`：`00000000-0000-1000-8000-00805F9B34FB`。利用该基础`UUID`，可以使用`16 bit`或`32 bit`的`UUID`来代替`128 bit`的`UUID`，当传递到对端设备，再还原成`128 bit`的`UUID`。
&emsp;&emsp;假如`16 bit`的`UUID`为`YYYY`，则还原后的`128 bit`的`UUID`为`0000YYYY-0000-1000-8000-00805F9B34FB`。
&emsp;&emsp;假如`32 bit`的`UUID`为`YYYYYYYY`，则还原后的`128 bit`的`UUID`为`YYYYYYYY-0000-1000-8000-00805F9B34FB`。
&emsp;&emsp;`ATT`层支持使用`16 bit`和`128 bit`两种`UUID`。而`32 bit`的`UUID`在使用前必须转换成`128 bit`。

### 协议方法(Protocol Methods)

&emsp;&emsp;对`Attribute`的操作称为协议方法，包括：

- 命令(`Command`)
- 请求(`Request`)
- 响应(`Response`)
- 通知(`Notification`)
- 指示(`Indication`)
- 确认(`Confirmation`)

### 长包属性(Long Attribute Values)

&emsp;&emsp;对于读属性的数据包，最大长度为(`ATT_MTU – 1`)个字节，其中减去的`1`表示`1`字节的操作码。
&emsp;&emsp;对于写属性的数据包，最大长度为(`ATT_MTU – 3`)个字节，其中减去的`3`表示`1`字节的操作码和`2`字节的属性句柄。
&emsp;&emsp;如果数据包超过这个长度，则称为`长包属性`。
&emsp;&emsp;读长包属性，需要使用`Read Blob Request`；写长包属性，需要使用`Prepare Write Request`和`Execute Write Request`。
&emsp;&emsp;如果使用普通`Read`操作读长包属性，仅能读取前(`ATT_MTU – 1`)个字节，使用普通`Write`操作写长包属性，仅能写前(`ATT_MTU – 3`)个字节。
&emsp;&emsp;无论是普通属性还是长包属性，属性值的最大长度均为`512`字节。

### 原子操作(Atomic Operations)

&emsp;&emsp;一个请求或一个命令称为一个`原子操作`。一个原子操作结束后，才能进行新的原子操作。读写长包属性无法在一个原子操作内完成。

### 属性PDU(Attribute PDUs)

&emsp;&emsp;在`ATT`层协议框架内，拥有一组属性的设备称为`服务端`(`ATT Server`)，读写该属性值的设备称为`客户端`(`ATT Client`)。
&emsp;&emsp;属性`PDU`有`6`类，如下所示：

属性PDU      | 方向              | 触发响应
-------------|------------------|--------
Command      | Client -> Server | -
Request      | Client -> Server | Response
Response     | Server -> Client | -
Notification | Server -> Client | -
Indication   | Server -> Client | Confirmation
Confirmation | Client -> Server | -

&emsp;&emsp;属性`PDU`格式如下图所示：

<img src="./Attribute Protocol/Attribute PDUs的格式.png" width=60%>

- `Opcode`：

1. 第`0`至`5`位：表示该属性的具体类型。
2. 第`6`位：表示命令标志位，如果该位为`1`，表示该操作码对应一个命令。
3. 第`7`位：表示认证签名(`Authentication Signature`)标志位，如果该位为`1`，表示该`PDU`的最后一个字段中包含`12`字节的认证签名。

- `Parameter`：其长度为`0`至`ATT_MTU – X`。如果认证签名位为`1`，则此处`X`等于`13`，否则等于`1`。
- `Authentication Signature`：只有写命令才需要`Authentication Signature`，其他命令不需要。此外，如果链路已经进行加密，则属性`PDU`中也无需额外添加`Authentication Signature`。

### 操作顺序

&emsp;&emsp;对于`Request`和`Indication`属性，需要接收端返回响应。在发出`Request`和`Indication`后，收到响应之前，不能发出新的`Request`和`Indication`。
&emsp;&emsp;对于其他无需响应的属性，则可以自由发送，但是不保证接收端一定能够收到和执行。
&emsp;&emsp;可以在`Request`和`Response`之间，或`Indication`和`Confirmation`之间发送其他无需响应的属性。

### 事务

&emsp;&emsp;一个`Request-Response`对，或`Indication-Confirmation`对，称为一个事务。
&emsp;&emsp;对于客户端设备而言，发出`Request`或收到`Indication`表示事务的开始，收到`Response`或返回`Confirmation`表示事务的结束。
&emsp;&emsp;对于服务端设备而言，发出`Indication`或收到`Confirmation`表示事务的开始，收到`Confirmation`或返回`Response`表示事务的结束。

### 属性协议PDU(attribute protocol PDUs)

&emsp;&emsp;属性协议规定了多种`Request-Response`对，`Request`属性由客户端设备发出，`Response`属性由服务端设备发出。

### 错误处理(Error Response)

&emsp;&emsp;如果属性`PDU`的操作码无效，或属性句柄无效，将返回错误响应`PDU`。

### 交换MTU(Exchange MTU Request)

&emsp;&emsp;`ATT_MTU`表示`ATT`层间传输的数据包的最大长度。两端设备可以通过`Exchange MTU Request/Response`进行交换`MTU`。
&emsp;&emsp;客户端设备向服务端设备发送交换`MTU`请求，提供客户端设备的`MTU`值；服务端设备获知客户端的`MTU`值，并返回自己的`MTU`值。两端设备都将设置较小的`MTU`值作为新的`MTU`值。
&emsp;&emsp;如果两端设备没有交换`MTU`，则使用默认的`MTU`值(`BLE`规定为`23`)处理属性事务。

Opcode | PDU
-------|-----
0x02   | Exchange MTU Request
0x03   | Exchange MTU Response

<img src="./Attribute Protocol/交换MTU的过程.png" width=40%>

### 查找信息(Find Information)

&emsp;&emsp;查找信息请求(`Find Information Request`)包含`2`个参数：`起始属性句柄`和`结束属性句柄`，用于获取服务端设备属性句柄处于该参数区间内的属性。
&emsp;&emsp;查找信息响应(`Find Information Response`)返回指定句柄区间内的属性`UUID`。如果区间内有多个属性，则返回多个响应。
&emsp;&emsp;按类型值查找请求(`Find By Type Value Request`)是在`Find Information Request`的基础上，加上了属性类型和属性值两个参数，这样能够更加精确的找到目标属性。
&emsp;&emsp;按类型值查找响应(`Find By Type Value Response`)包含了满足条件的属性句柄列表。

### 读属性(Reading Attributes)

&emsp;&emsp;按类型读请求(`Read By Type Request`)包含`3`个参数：`起始属性句柄`、`结束属性句柄`和`属性类型`。该请求用于在属性类型已知，但句柄未知的情况下获取属性值。
&emsp;&emsp;按类型读响应(`Read By Type Response`)包含了满足该条件的`handle-value`列表。
&emsp;&emsp;读请求(`Read Request`)用于向服务器请求读取属性的值。
&emsp;&emsp;读响应(`Read Response`)返回满足条件的属性值。
&emsp;&emsp;读片段请求(`Read Blob Request`)用于请求服务器以给定的偏移量读取属性值的一部分(读取一个长包属性的值)。以不同的偏移量作为参数，多次执行该请求可以读取长包属性的完整值。
&emsp;&emsp;读片段响应(`Read Blob Response`)包含了长包属性值的指定偏移量片段。
&emsp;&emsp;读多次请求(`Read Multiple Request`)用于读取多个给定句柄的属性值。
&emsp;&emsp;读多次响应(`Read Multiple Response`)包含了多个指定句柄的属性值。
&emsp;&emsp;按组类型读请求(`Read By Group Type Request`)用于读取指定组类型的属性值，组类型是由`ATT`层之上的协议层设定的。
&emsp;&emsp;按组类型读响应(`Read by Group Type Response`)包含了满足条件的属性值列表。

### 写属性(Writing Attributes)

&emsp;&emsp;写请求(`Write Request`)将待写数值写入指定的属性。
&emsp;&emsp;写响应(`Write Response`)表示写请求执行成功，不含任何参数。
&emsp;&emsp;写命令(`Write Command`)将待写数值写入指定的属性(通常是`control-point`属性)，它不会触发一个写响应。
&emsp;&emsp;签名的写命令(`Signed Write Command`)与上面的`Write Command`类似，用于请求服务器将带有身份验证签名的属性值(通常是`control-point`属性)写入。