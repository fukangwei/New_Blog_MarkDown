---
title: Generic Access Profile
date: 2020-11-16 06:50:25
categories: BLE笔记
---
### Generic Access Profile(GAP)

&emsp;&emsp;`BLE`使用`GAP`来约定如何发现设备、连接设备，它是一种`Profile`(规范)。这个规范里要用到`LL`、`L2CAP`、`SM`、`ATT`以及`GATT`层的功能。<!--more-->

&emsp;&emsp;`GAP`可以实现如下功能：

### 定义GAP层的蓝牙设备角色

&emsp;&emsp;和`Link Layer`的`Role`类似，只不过`GAP`层的`Role`更接近用户(可以等同于从用户的角度看到的蓝牙设备的`Role`)，包括：

- `Broadcaster`：可以发送广播和接收扫描请求，通常不建立连接，对应链路层的广播状态。
- `Observer`：可以扫描广播和发起扫描请求，通常不建立连接，对应链路层的扫描状态。
- `Peripheral`：可以发送广播数据和接收连接请求，进而建立连接，对应链路层的广播状态(对应`Link Layer`的`Slave`角色)。
- `Central`：扫描广播数据和发送连接请求，进而建立连接，对应链路层的扫描状态或发起状态(对应`Link Layer`的`Master`角色)。

&emsp;&emsp;由于链路层支持同时拥有多个状态机，`GAP`层也支持一个设备同时具有多个`GAP`角色，比如在一个连接中充当`Central`设备，同时对外发出广播充当`Peripheral`设备。

&emsp;&emsp;设想如下`4`种设备：

1. 超市货架：每一个货架都有一个蓝牙设备，它不断向周边发出广告信息，你肯定不想连接它。
2. 用电池供电的手环：平时为了省电什么都不做，如果要发现并连接它，首先按一下按钮。
3. 家里用插座供电的闹钟：它不需要省电，可以一直发出广播信息。
4. 你的手机。

&emsp;&emsp;上述设备的角色(`Role`)、所处的模式(`Mode`)、能执行的操作或规程(`Procedure`)是由`GAP`定义。

- 货架上的蓝牙设备：它的角色是`Broadcaster`(广播者)，只广播信息，别人不可能也没必要去连接它。
- 手环：它的角色是`Peripheral`(外围设备)，它可以处于`Limited Discoverable Mode`(有限可发现模式)。在按下按钮的一小段时间里，可以用手机去发现它；超时之后，手机无法发现它。对应的，手机的`Role`是`Central`(中央设备)，它要执行这`2`种规程(`Procedure`)之一才能发现这个手环：

1. `Limited Discovery Procedure`
2. `General Discovery Procedure`

- 闹钟：它的角色是`Peripheral`(外围设备)，可以处于`General Discoverable Mode`(普通可发现模式)，一直发出广播信号。对应的，手机的`Role`是`Central`(中央设备)，它要执行`General Discovery Procedure`才能发现这个闹钟。
- 手机：既可能是`Observer`，也可能是`Central`。当它去获得货架广告机的信息时，它是`Observer`；当它去尝试连接手环、闹钟时，它是`Central`。

### 定义操作模式和规程

&emsp;&emsp;`Mode`是指一个设备被配置为按照某种方式操作一段较长时间；`Procedure`是指有限的时间内执行某种特定的操作。
&emsp;&emsp;`GAP`定义了如下的操作模式(`Operational Mode`)和规程(`Procedures`)：

#### 播报模式和观察规程

&emsp;&emsp;播报模式(`Broadcast Mode`)并不等同于普通的广播状态。在播报模式下，设备在广播事件中发送不可连接的广播数据，播报设备可以响应外部的扫描请求。
&emsp;&emsp;播报设备的广播数据格式与普通的广播数据相同，但是它不设置`LE Limited Discoverable Mode`和`LE General Discoverable Mode`这两个标志位，即播报设备是`Non-Discovery`设备，这意味着中央设备扫描到该广播数据，应该选择忽略。
&emsp;&emsp;观察规程(`Observer Procedure`)用于监听播报设备的广播数据和扫描响应数据，也可以监听普通广播设备。`Observation Procedure`有`2`种方法：主动扫描(`Active Scanning`)、被动扫描(`Passive Scanning`)。

#### 可发现模式和发现规程

&emsp;&emsp;可发现模式(`Discovery Mode`)分为`3`种：

&emsp;&emsp;有限发现模式将广播数据的`LE Limited Discoverable Mode`位置为`1`，普通发现模式将广播数据的`LE General Discoverable Mode`位置为`1`。如果这`2`个标志位均不设置，就是不可发现模式。
&emsp;&emsp;不可发现模式的广播数据与其他两种模式相同，所以其广播数据仍然能够被扫描设备正确读取。但由于没有设置相应的标志位，扫描设备在解析广播数据时应该尊重其不愿意被发现的意图，主动忽略该广播数据。
&emsp;&emsp;使用观察规程(`Discovery Procedure`)的观察设备不会忽略不可发现模式的广播数据。
有限发现模式通常用于用户指定的行为让设备临时进入可发现状态，可发现状态持续时间为`T_GAP[lim_adv_timeout]`；普通发现模式是默认模式，它没有时间限制。
&emsp;&emsp;发现规程分为`2`种：

- **有限发现规程**：仅处理有限发现模式下的广播数据，包括设备地址和广播数据，忽略其他发现模式下的广播设备。
- **常规发现规程**：能处理普通发现模式和有限发现模式下的广播数据。

&emsp;&emsp;此外还有一种发现规程，专用于发现设备名称：

&emsp;&emsp;设备名发现规程可以发现普通发现模式和有限发现模式下的广播设备名称。发现设备名称的步骤如下：

1. 建立连接。
2. 读取`GATT`中的名字特征值。

#### 可连接模式和连接规程

&emsp;&emsp;可连接模式(`Connection Mode`)分为`3`种：

1. `Non-Connectable Mode`：不可连接模式。
2. `Directed Connectable Mode`：定向连接模式，如果希望快速连接中央设备，外围设备可以使用该模式。在该模式下，它发出`ADV_DIRECT_ADV`广播报文，直接发给指定的中央设备。所以，该外围设备应该曾经与该中央设备建立过连接。`ADV_DIRECT_ADV`广播报文的发送频率很快，该模式最多持续`1.28s`，之后控制器自动停止广播。
3. `Undirected Connectable Mode`：无向连接模式。如果一个外围设备无需快速建立连接，或者打算尽可能地省电，则可以使用该模式。它发出`ADV_IND`广播报文。

&emsp;&emsp;而相关的连接规程(`Connection Procedure`)则有`4`种：

1. `Auto Connection Establishment Procedure`：自动连接建立规程。中央设备使用该规程来设置`Controller`，从而向一个或多个外围设备自动地建立连接。`Host`需要事先把想要连接的设备写入白名单中。它有个缺点：对所有的设备，都只能使用一套基本连接参数。
2. `General Connection Establishment Procedure`：一般连接建立规程。这是我们常用的规程，先扫描出设备，选中某一个，然后使用`Direct Connection Establishment Procedure`去连接它。
3. `Selective Connection Establishment Procedure`：选择性连接建立规程。`Host`先把想连接的设备放入`Controller`的白名单。正在广播的设备，只有在出现在白名单上时才会被提交给`Host`。`Host`决定是否连接该设备，并使用不同的参数去连接它。相比于`Auto Connection Establishment Procedure`，`Selective Connection Establishment Procedure`可以使用不同的参数连接每一个设备。
4. `Direct Connection Establishment Procedure`：定向连接建立规程，直接去连接指定的设备。

&emsp;&emsp;此外，还有`2`个与连接相关的规程：

1. `Connection Parameter Update Procedure`：连接参数更新规程。
2. `Terminate Connection Procedure`：终止连接规程。中央设备或外围设备都可以执行该规程，通过`LL`层向对方发出`LL_TERMINATE_IND PDU`就可以。

#### 可绑定模式和绑定规程

&emsp;&emsp;可绑定模式(`Bonding Mode`)分为如下`2`种：

#### 周期广播模式和周期广播规程

&emsp;&emsp;周期广播模式(`Periodic Advertising Mode`)分为如下`2`种：

&emsp;&emsp;周期广播规程(`Periodic Advertising Procedure`)有如下`1`种：

#### 安全模式和认证规程

&emsp;&emsp;安全模式分为如下`2`种：

&emsp;&emsp;安全模式`1`下有`4`种安全级别：

1. `No security`(`No authentication and no encryption`)
2. `Unauthenticated pairing with encryption`
3. `Authenticated pairing with encryption`
4. `Authenticated LE Secure Connections`

&emsp;&emsp;`4`种安全级别围绕着认证和加密进行，安全级别依次增加。第`1`种安全级别没有认证和加密，第`2`种安全基本提供未认证的加密，第`3`、`4`种安全级别能够提供认证和加密。
&emsp;&emsp;安全模式`2`下有`2`种安全级别：

1. `Unauthenticated pairing with data signing`
2. `Authenticated pairing with data signing`

&emsp;&emsp;安全规程分为如下`4`种：

#### 隐私规程

&emsp;&emsp;隐私与私有地址有密切关系，跟私有地址相关的规程如下：

### 定义与用户操作相关的参数

#### 设备地址

&emsp;&emsp;设备地址在协议栈内部指`BD_ADDR`，在用户界面显示为`Bluetooth Device Address`。
&emsp;&emsp;设备地址为一个`6`字节的整形数组成，可以用冒号作为分隔符，比如`00:0C:3E:3A:4B:69`。
&emsp;&emsp;在用户界面，设备地址以自然顺序显示，而内部的`BD_ADDR`则以逆序保存，对于上述地址，`BD_ADDR[0]`等于`0x69`而不是`0x00`。

#### 设备名

&emsp;&emsp;设备名称仅起识别设备的作用，在用户界面显示为`Bluetooth Device Name`。设备名最长可达`248`个字节，但是对端设备可能并不能显示这么长的名称。设备名支持`UTF-8`编码，因此设备名可以使用中文。

#### PIN码

&emsp;&emsp;`PIN`码指两个设备配对时使用的`passkey`密码，在用户界面显示为`Bluetooth Passkey`。
&emsp;&emsp;`PIN`码为`6`位十进制整形数，因此它的有效范围为`000000 - 999999`(`0x00000000 - 0x000F423F`)。使用时必须显示全部`6`位数字，包括前导`0`。

#### 设备外观

&emsp;&emsp;设备外观仅起识辅助识别设备的作用，在用户界面显示为一个图标或一个字符串。设备外观为一个`2`字节数，扫描设备可以通过设备外观值为设备分配一个合适的图标或描述。

### GAP服务

&emsp;&emsp;每一个中央设备或外围设备中，都有一个`GAP Service`，它包含如下特征项(`Characteristic`)：

&emsp;&emsp;最重要`2`项`Characteristic`如下：

- `Device Name`：设备名字。
- `Appearance`：中央设备根据它来显示一个图标、字符串等，用来形象地描述设备的功能。