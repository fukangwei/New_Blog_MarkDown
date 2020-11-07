---
title: 安全机制之SM
date: 2020-12-07 21:32:53
categories: BLE笔记
---
&emsp;&emsp;`BLE`安全机制中的终极武器是加密，不过使用这把武器有个前提，那就是双方要共同拥有一个加密`Key`(`Long Term Key`，`LTK`)。这个`Key`至关重要，怎么生成、怎么由通信的双方共享，关系到加密的成败。因此蓝牙协议定义了一系列复杂机制，用于处理有关于加密`Key`的操作，这就是`SM`(`Security Manager`)。<!--more-->
&emsp;&emsp;`SM`在蓝牙协议中的位置如下图所示：

&emsp;&emsp;它的主要目的是为`LE`设备(`LE Only`或者`BR/EDR/LE`)提供建立加密连接所需的`Key`(`STK or LTK`)。为了达到这个目的，它定义了如下几类规范：

- 将生成加密`Key`的过程称为`Pairing`(配对)，并详细定义了`Pairing`的概念、操作步骤、实现细节等。
- 定义一个密码工具箱(`Cryptographic Toolbox`)，其中包含了配对、加密等过程中所需的各种加密算法。
- 定义一个协议(`Security Manager Protocol`，`SMP`)，基于`L2CAP`连接，实现`Master`和`Slave`之间的配对、密码传输等操作。

### Pairing(配对)

&emsp;&emsp;两端设备先建立连接，然后才能进行配对操作。配对无需在连接后立即执行，可以在任何需要时候进行。
&emsp;&emsp;配对的过程主要由`3`个阶段组成，如下图所示：

&emsp;&emsp;阶段`1`称作`Pairing Feature Exchange`，用于交换双方有关鉴权的需求(`Authentication Requirements`)，以及双方具有怎样的人机交互能力(`IO Capabilities`)。
&emsp;&emsp;阶段`2`通过`SMP`协议进行实际的配对操作，根据阶段`1`的`Feature Exchange`结果，有`2`种配对方法可选：`LE Legacy Pairing`和`LE Secure Connections`。
&emsp;&emsp;阶段`3`是可选的，经过阶段`1`和阶段`2`之后，双方已经产生了加密`Key`，因而可以建立加密的连接。加密连接建立后，可以互相传送一些私密的信息，例如`Encryption Information`、`Identity Information`、`Identity Address Information`等。

### 安全特性

&emsp;&emsp;安全特性取决于设备的认证需求，可选的安全特性如下图所示：

&emsp;&emsp;`MITM`(`Man in the Middle`)指中间人攻击，假如第三方设备攻破了`BLE`连接，`A`设备发送的消息被`C`设备接收，`C`设备再转发给`B`设备，`A`与`B`设备相互以为建立了连接，而实际上所有的数据通信都经过了`C`设备转发。
&emsp;&emsp;前两种安全特性可以实现`MITM`保护，后两种则无法防护`MITM`攻击。四种安全等级从上至下安全性依次降低。安全特性信息会持久保存在设备的安全数据库中。

### Pairing Feature Exchange

&emsp;&emsp;配对的过程总是以`Pairing Request`和`Pairing Response`的协议交互开始，通过这两个命令，配对的发起者(`Initiator`，总是`Master`)和配对的回应者(`Responder`，总是`Slave`)可以交换足够的信息，以决定在阶段`2`使用哪种配对方法、哪种鉴权方式。
&emsp;&emsp;`Master`和`Slave`有`2`种可选的配对方法：`LE Legacy Pairing`和`LE Secure Connections`，其中前者是过去的方法，后者是新方法。选择的依据是：当`Master`和`Slave`都支持`LE Secure Connections`(新方法)的时候，则使用`LE Secure Connections`；否则，使用`LE Legacy Pairing`。

### 鉴权方法

&emsp;&emsp;所谓的鉴权(`Authentication`)，就是要保证执行某一操作的双方(或者多方，这里就是配对的双方)的身份合法性。对`BLE`来说，主要有`2`类鉴权的方法：

1. 配对的双方在配对过程之外，需要交互一些额外的信息，并以这些信息为输入，进行后续的配对操作。这些额外信息也称作`OOB`(`Out Of Band`)，`OOB`的交互过程称为`OOB Protocol`。
2. 让人参与进来：例如手机`A`向手机`B`发起配对操作时，手机`A`显示一串`6`位数字的配对码，并将该配对码发送给手机`B`；手机`B`显示同样的配对码，并要求用户确认`A`和`B`显示的配对码是否相同。如果相同，在`B`设备上点击配对即可配对成功。

&emsp;&emsp;这种需要人参与的鉴权方式，在蓝牙协议中称作`MITM`(`Man-In-The-Middle`)。不过由于`BLE`设备的形态千差万别，硬件配置也各不相同，有些可以输入可以显示、有些只可输入不可显示，因此无法使用统一的方式进行`MITM`鉴权(例如，没有显示的设备无法使用上面的方式进行鉴权)。为此`Security Manager`定义了多种交互方法：
&emsp;&emsp;`Passkey Entry`：通过输入配对码的方式鉴权，有`2`种操作方法：

1. 用户在两个设备上输入相同的`6`个数字(要求两个设备都有数字输入的能力)，接下来的配对过程会进行相应的校验。
2. 设备`A`随机生成并显示`6`个数字(要求该设备有显示能力)，用户记下这个数字，并在设备`B`上输入。设备`B`在输入的同时，会通过`SMP`协议将输入的数字同步地传输给设备`A`，设备`A`会校验数字是否正确，以达到鉴权的目的。

&emsp;&emsp;`Numeric Comparison`：两个设备自行协商生成`6`个数字，并显示出来(要求两个设备具有显示能力)，用户比较后进行确认(一致或者不一致，要求设备有简单的`Yes or No`的确认能力)。
&emsp;&emsp;`Just Works`：不需要用户参与，两个设备自行协商。
&emsp;&emsp;在`Pairing Feature Exchange`阶段，配对的双方以下面的原则选择鉴权方法：

- 如果双方都支持`OOB`鉴权，则选择该方式(优先级最高)。
- 否则，如果双方都支持`MITM`鉴权，则根据双方的`IO Capabilities`，并结合具体的配对方法，选择合适的鉴权方式。
- 否则，使用`Just Works`的方式(不再鉴权)。

&emsp;&emsp;如果采用了`Just Works`方式，一定是未认证的；`Passkey Entry`和`Numeric Comparison`方式则是认证的。

### IO Capabilities

&emsp;&emsp;`IO`的能力可以归纳为如下的`6`种：

- `NoInputNoOutput`
- `DisplayOnly`
- `NoInputNoOutput1`
- `DisplayYesNo`
- `KeyboardOnly`
- `KeyboardDisplay`

### LE legacy pairing

&emsp;&emsp;`LE legacy pairing`的过程比较简单：

&emsp;&emsp;1. `LE legacy pairing`最终生成的是`Short Term Key`(`STK`)，由双方共享。用`STK`充当`LTK`，并将`EDIV`和`Rand`设置为`0`，去建立加密连接。
&emsp;&emsp;2. 加密连接建立之后，双方可以自行生成`Long Term Key`，以及相应的`EDIV`和`Rand`，并通过后续的`Transport Specific Key Distribution`将它们共享给对方，以便后面重新建立加密连接。
&emsp;&emsp;我们可以思考一个问题：为什么`LE legacy pairing`的`LTK`需要`EDIV/Rand`信息呢？因为`LTK`是各自生成的，一般情况下是不一样的，因而需要一个索引去查找某一个`LTK`(对比后面介绍的`LE Secure Connections`，`LTK`是直接在配对时生成的，因而就不需要这两个东西)。
&emsp;&emsp;3. `STK`的生成也比较简单，双方各提供一个随机数(`MRand`和`SRand`)，并以`TK`为密码，执行`S1`加密算法即可(`TK`的意思是临时密码)。
&emsp;&emsp;4. `TK`是在鉴权的过程中得到的，根据在阶段`1`选择的鉴权方法，`TK`可以是通过`OOB`得到，也可以是通过`Passkey Entry`得到，也可以是`0`(`Just Works`)。
&emsp;&emsp;`LE legacy pairing`只能使用`OOB`、`Passkey Entry`或者`Just Works`这些鉴权方法；`Numeric Comparison`只能在`LE Secure Connections`中使用。

### LE Secure Connections Pairing

&emsp;&emsp;`LE Secure Connections pairing`利用了椭圆曲线加密算法(`P-256 elliptic curve`)，简单说明如下：

- 可以使用`OOB`、`Passkey Entry`、`Just Works`以及`Numeric Comparison`这些鉴权方法。其中`Numeric Comparison`的流程和`Just Works`基本一样。
- 可以直接生成`LTK`(双方共享)，然后直接使用`LTK`进行后续的链路加密，以及重新连接时的加密。

### Transport Specific Key Distribution

&emsp;&emsp;加密链路建立之后，通信的双方就可以传输一些比较私密的信息，主要包括：

- `Encryption Information`(`Long Term Key`)
- `Master Identification`(`EDIV`和`Rand`)
- `Identity Information`(`Identity Resolving Key`)
- `Identity Address Information`(`AddrType`和`BD_ADDR`)
- `Signing Information`(`Signature Key`)