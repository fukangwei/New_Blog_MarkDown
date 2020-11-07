---
title: Generic Attribute Profile
date: 2020-11-10 21:33:18
categories: BLE笔记
---
### GATT的作用

&emsp;&emsp;`ATT`之所以称作`Protocol`，是因为它还比较抽象，仅仅定义了一套机制，允许`Client`和`Server`通过`Attribute`的形式共享信息。而具体共享哪些信息，`ATT`并不关心，这是`GATT`(`Generic Attribute Profile`)的主场。<!--more-->
&emsp;&emsp;`GATT`相对于`ATT`只多了一个`G`，但含义却大不同，因为`GATT`是一个`Profile`(更准确的说是`Profile Framework`)。在蓝牙协议中，`Profile`一直是一个比较抽象的概念，我们可以将其理解为应用场景、功能、使用方式都被规定好的`Application`。`GATT`就是在`ATT`的基础上做`2`件事：

1. 如何定义服务
2. 如何访问服务

&emsp;&emsp;`GATT`通信的双方是`Client-Server`关系，外设作为`GATT Server`，它维护了`ATT`的查找表以及`Service`和`Characteristic`的定义。中心设备是`GATT Client`，它向`Server`发起请求。需要注意的是，所有的通信事件都是由`GATT Client`(即`Master`)发起，并且接收`GATT Server`(即`Slave`)的响应。一旦两个设备建立起了连接，`GATT`就开始起作用了，这也意味着，你必需首先完成前面的`GAP`协议。`GATT`规范定义了客户端设备发现服务端设备的服务项的方法，建立连接以后，客户端设备可以通过发现方法检索服务端设备的`GATT`服务项和特征项，进而发送命令或数据。服务端向客户端发送数据以通知和指示的形式发送，客户端收到指示信息需要返回确认信息。
&emsp;&emsp;作为一个`Profile Framework`，`GATT`提出了如下的层次结构：

<img src="./Generic Attribute Profile/GATT的层次结构.png" width=40%>

&emsp;&emsp;由此得知，`GATT`的层次结构依次是：

``` cpp
Profile –> Service -> Characteristic
```

- `Profile`是基于`GATT`所派生出的真正的`Profile`，位于`GATT`的最顶层，由一个或者多个和某一应用场景有关的`Service`组成。
- 一个`Service`包含一个或者多个`Characteristic`(特征)，也可以通过`Include`的方式，包含其它`Service`。
- `Characteristic`则是`GATT`中最基本的数据单位，由一个`Properties`、一个`Value`、一个或者多个`Descriptor`组成。

&emsp;&emsp;1) `Properties`定义了`Characteristic`的`Value`如何被使用，以及`Characteristic`的`Descriptor`如何被访问。
&emsp;&emsp;2) `Value`是特征的实际值，例如一个温度特征，其`Value`就是温度值。
&emsp;&emsp;3) `Descriptor`则保存了一些和`Value`相关的信息。

&emsp;&emsp;以上除`Profile`外的每一个定义：`Service`、`Characteristic`、`Properties`、`Value`、`Descriptor`等，都是作为一个`Attribute`存在的。
&emsp;&emsp;设备可以同时属于客户端和服务端。`GATT`角色与执行过程相关，它不与设备绑定。设备在执行一个过程时，根据发起命令或接收命令而决定它是服务端还是客户端，该过程结束后就释放`GATT`角色。
&emsp;&emsp;`GATT`角色不与链路层的`Master`和`Slave`角色绑定。一个链路层的`Master`通常担任`GATT`客户端角色，也可以担任`GATT`服务端角色。

### 属性缓存

&emsp;&emsp;客户端与服务端建立连接后，执行发现过程，以获取服务端所携带的全部属性。属性缓存功能用于保存服务端设备的属性句柄，使下一次重新连接时无需执行发现过程。
&emsp;&emsp;一般情况下，服务端设备的属性不会改变，但是执行固件升级则可以改变设备的属性。
&emsp;&emsp;如果改变设备的属性，将从`Service Changed characteristic`发出一个指示`PDU`，告知客户端设备：服务端设备的属性发生了改变。该指示`PDU`中包含了发生改变的属性句柄范围，客户端设备收到该指示，重新执行发现过程，获取更新后的服务端设备的属性句柄。
&emsp;&emsp;如果设备的属性确定不能发生改变，则无需增加`Service Changed characteristic`属性。
&emsp;&emsp;如果两端设备完成绑定，则属性缓存信息一直有效，直到收到了`Service Changed characteristic`发出的指示。如果在服务端的属性在断开后发生了改变，则服务端在下次重连时候，发送指示给客户端设备重新缓存属性句柄。

### 服务项

&emsp;&emsp;一个`Service`的定义是从它的`Declaration`开始，到下一个`Service`的`Declaration`结束。所谓的`Declaration`，实际上就是一个属性：

- 属性的`Type`为`0x2800`或`0x2801`，表示这是一个首要服务(`Primary Service`)或次要服务(`Secondary Service`)。主要服务项可以独立使用，次要服务项一定要被其他服务项包含引用。
- 属性的`Value`用来表示这是什么服务，即`Value`是某个服务的`UUID`。
- 属性的`Permissions`表示该声明即这个`ATT`属性是只读的，无需验证、无需授权。

&emsp;&emsp;从这里可以知道，蓝牙设备能提供什么服务是公开的，它无法隐瞒，别的设备都可以来查询该设备提供什么服务。
&emsp;&emsp;服务项必须包含一个服务项声明，可选地包含多个其他服务项和特征项。所包含的其他服务项和特征项均是该服务项的一部分。服务项的声明格式如下：

<img src="./Generic Attribute Profile/服务项.png" width=60%>

&emsp;&emsp;协议栈文档中对次要服务项的使用场景解释有限，在绝大多数情况下均可以不使用次要服务项，仅使用主要服务。

### 包含(Include)

&emsp;&emsp;`Include`体现了一个引用机制，比如需要扩展一个现有的服务项，可以在新的服务项中引用该服务项。假如服务项中`Include`了其他服务项，则需要加入`Include`的声明(`UUID = 0x2802`)。
&emsp;&emsp;协议栈文档中对`Include`的使用场景解释有限，在绝大多数情况下均可以不使用`Include`功能。

### 特征项

&emsp;&emsp;一个`Characteristic`定义，从它的`Declaration`开始，到下一个`Characteristic`的`Declaration`结束，或是直到下一个`Service`的`Declaration`结束。
&emsp;&emsp;`Characteristic`的`Declaration`是一个`ATT`属性，其`Type`为`0x2803`。`Value`含有`3`项内容：

- `Characteristic Properties`：特性的性质。
- `Characteristic Value Attributes Handle`：表示`Characteristic Value Attributes`的`ATT`属性`Handle`。
- `Characteristic UUID`：表示`Characteristic Value`的`ATT`属性的`UUID`。

&emsp;&emsp;可以看出`Characteristic Value`的`Declaration`也是一个`ATT`属性。

<img src="./Generic Attribute Profile/特征项.png" width=60%>

&emsp;&emsp;特征值功能特性(`Characteristic Properties`)如下表所示：

特征值功能                    | 值     | 描述
------------------------------|--------|-----
`Broadcast`                   | `0x01` | 允许广播该特征值
`Read`                        | `0x02` | 允许读该特征值
`Write Without Response`      | `0x04` | 允许写该特征值，不需要`Response`
`Write`                       | `0x08` | 允许写该特征值，需要`Response`
`Notify`                      | `0x10` | 允许该特征值发送通知
`Indicate`                    | `0x20` | 允许该特征值发送指示
`Authenticated Signed Writes` | `0x40` | 允许带认证签名的写该特征值
`Extended Properties`         | `0x80` | 拓展特性

&emsp;&emsp;其中，`Broadcase`(`0x01`)、`Notify`(`0x10`)和`Indicate`(`0x20`)要求该特征值具有服务端特征项配置描述符(`CCCD`)。

### 描述符

&emsp;&emsp;描述符也是一种属性，它是特征项的一部分，用于提供特征值的额外信息。协议栈定义了`6`种不同的描述符：

属性类型                              | UUID     | 描述
--------------------------------------|----------|-----
`Characteristic Extended Properties`  | `0x2900` | 特征项的拓展描述符
`Characteristic User Description`     | `0x2901` | 特征项的用户描述符
`Client Characteristic Configuration` | `0x2902` | 客户端特征项配置描述符
`Server Characteristic Configuration` | `0x2903` | 服务器特征项配置描述符
`Characteristic Format`               | `0x2904` | 特征项数据格式描述符
`Characteristic Aggregate Format`     | `0x2905` | 聚合特征项数据格式描述符

- `0x2900`(扩展性描述符)：用于`Reliable Write`和`Writable Auxiliaries`这`2`类写属性。
- `0x2901`(用户描述符)：用于给出该特征值的文字描述。
- `0x2902`(客户端特征项配置描述符)：简称为`CCCD`，客户端设备通过一个标志参数，设置该特征值能否发送通知和指示。如果该标志参数为`0x0001`，表示该特征值允许发送通知；如果该标志参数为`0x0002`，表示该特征值允许发送指示；如果该标志参数为`0x0000`，表示该特征值不能发送通知和指示。

&emsp;&emsp;每个特征项最多能包含一个`CCCD`，对于具有`Broadcast`、`Notify`和`Indicate`功能的特征项必须拥有一个`CCCD`。在两个建立了绑定的设备中，断开连接不会丢失`CCCD`信息。

- `0x2903`(服务端特征项配置描述符)：服务端设备通过一个标志参数，设置该特征值是否在广播中发出。如果该标志参数为`0x0001`，则广播消息中应该包含该特征值；如果该标志参数为`0x0000`，则广播消息中不包含该特征值。
- `0x2904`(特征值格式描述符)：用于提供特征值的数据格式。可选的数据类型包括：`Boolean`、`1/4`字节、`1/2`字节、`1`字节、`2`字节、`3`字节、`4`字节、`8`字节、`16`字节、带符号整数、无符号整数、浮点数、字符串、结构体等。还可以指定数据的指数、单位、名字空间、描述信息等。
- `0x2905`(聚合特征项格式描述符)：专用于聚合特征项。所谓聚合特征值，是指多个特征值共同组合成一个数值，每个特征值仅是该聚合数值的一部分。

### Service实例

&emsp;&emsp;下图是两个`Service`的实例：

<img src="./Generic Attribute Profile/Service实例.png" width=60%>

### GATT功能

&emsp;&emsp;`GATT`层定义了`11`项功能：

1. `Server Configuration`
2. `Primary Service Discovery`
3. `Relationship Discovery`
4. `Characteristic Discovery`
5. `Characteristic Descriptor Discovery`
6. `Reading a Characteristic Value`
7. `Writing a Characteristic Value`
8. `Notification of a Characteristic Value`
9. `Indication of a Characteristic Value`
10. `Reading a Characteristic Descriptor`
11. `Writing a Characteristic Descriptor`

&emsp;&emsp;这`11`项功能是通过`Procedure`来实现，而`Procedure`要通过`ATT`层的`Request`、`Response`等操作来传输数据。比如`Server Configuration`需要通过`Exchange MTU`这个规程来实现。

### 发现主要服务项

&emsp;&emsp;该功能包含`2`个子功能：

1. 发现全部主要服务项。
2. 按`UUID`发现主要服务项。

#### 全部主要服务项

&emsp;&emsp;该功能向服务端设备发送`Read By Group Type Request`，起始句柄为`0x0001`，结束句柄为`0xFFFF`，属性类型为`0x2800`(主要属性的`UUID`)，查找全部符合条件的首要服务项。
&emsp;&emsp;服务端返回`Read By Group Type Response`，该响应中包含多个属性信息组成的列表。单个属性信息包含`3`个参数：元素长度、属性组首尾句柄和属性的`UUID`。因为该列表长度可能超过`PDU`要求的最大长度，所以需要多次执行请求和响应，直到服务端设备返回未找到属性项或到达结束句柄，才能获取全部的主要服务项：

<img src="./Generic Attribute Profile/查找全部符合条件的主要服务项.png" width=60%>

&emsp;&emsp;观察上图，客户端第一次发起请求，查找主要服务项，首末句柄分别是`0x0001`和`0xFFFF`。
&emsp;&emsp;服务端返回响应中包含`3`个元素，每个元素代表一个首要服务项，每个元素的长度为`0x06`。

1. 第一个首要服务项的属性句柄为`0x0001`，类型为`UUID1`，末尾句柄为`0x000F`。
2. 第二个服务项的属性句柄为`0x0010`，类型为`UUID2`，末尾句柄为`0x0017`。
3. 第三个服务项的属性句柄为`0x0100`，类型为`UUID3`，末尾句柄为`0x01FF`。

&emsp;&emsp;客户端发起第二次请求，起始句柄设为`0x2000`。
&emsp;&emsp;服务端返回响应中仍然包含三个元素，每个元素的长度为`0x06`。三个元素分别表示三个首要服务项，其`UUID`分别为`UUID4`、`UUID5`和`UUID6`，`UUID6`的末尾句柄为`0x04FF`。
&emsp;&emsp;客户端接着发起第三次请求，起始句柄设为`0x0500`。
&emsp;&emsp;服务端返回错误，错误原因是未找到属性。客户端根据该错误原因，判断已经获取服务端设备的全部主要服务项。

#### 按UUID发现主要服务项

&emsp;&emsp;该功能向服务端设备发送`Read By Group Type Request`，起始句柄为`0x0001`，结束句柄为`0xFFFF`，属性类型为`0x2800`(主要服务声明的`UUID`)，指定的`UUID`为`xxxx`，查找全部符合条件的主要服务项。
&emsp;&emsp;具体的操作步骤与发现全部主要服务一致，通常具有指定`UUID`的服务项仅有一个。

### 发现特征项

&emsp;&emsp;该功能包含`2`个子功能：

1. 发现服务项下的全部特征项。
2. 按`UUID`发现特征项。

#### 发现服务项下的全部特征项

&emsp;&emsp;该功能向服务端设备发送`Read By Type Request`，设置已知的服务项首末句柄，属性类型为`0x2803`(特征项声明的`UUID`)，查找全部符合条件的特征项。
&emsp;&emsp;服务端返回`Read By Type Response`，该响应中包含多个`handle-value`元素组成的列表。单个元素包含`3`个参数：元素长度、特征项声明的句柄和特征值参数。特征值参数包括特征值的功能特性、特征值句柄和`UUID`。
&emsp;&emsp;因为该列表长度可能超过一个属性层的`PDU`长度，所以需要多次执行请求和响应，直到服务端设备返回未找到属性项或到达结束句柄，才能获取全部的特征项：

<img src="./Generic Attribute Profile/查找服务项下的全部特征项.png" width=60%>

&emsp;&emsp;观察上图，客户端第一次发起请求，查找特征项，首末句柄分别是`0x0200`和`0x0214`。
&emsp;&emsp;服务端返回响应中包含两个元素，每个元素代表一个特征项，每个元素的长度为`0x07`。

1. 第一个特征项的声明句柄为`0x0203`，特征值的功能特性为`0x02`，即具有`Read`功能，特征值的句柄为`0x0204`，特征值的`UUID`为`UUID1`。
2. 第二个特征项的声明句柄为`0x0210`，特征值的功能特性为`0x02`，即具有`Read`功能，特征值的句柄为`0x0212`，特征值的`UUID`为`UUID2`。

&emsp;&emsp;由于每个元素的长度为`7`，表明该两个特征值的`UUID`均是`2`字节`UUID`(`2 = 长度7 – 声明句柄长度2 – 功能特性1 – 特征值句柄2`)。如果是`16`字节`UUID`，则每个元素的长度应该为`0x15`(`声明句柄长度2 + 功能特性1 + 特征值句柄2 + UUID长度16`)。

#### 按UUID发现特征项

&emsp;&emsp;该功能根据已知的特征项`UUID`和首末句柄范围，查找满足条件的特征项。具体与发现服务项下的全部特征项完全一致。

### 发现描述符

&emsp;&emsp;该功能包含一个子功能：发现全部描述符。该功能向服务端设备发送`Find Information Request`，设置已知的特征项首末句柄，查找全部符合条件的描述符。
&emsp;&emsp;服务端返回`Find Information Response`，该响应中包含多个`handle-value`元素组成的列表。单个元素包含`3`个参数：`UUID`格式、特征值的句柄和描述符的`UUID`。

1. 如果`UUID`格式参数等于`1`，表示描述符的`UUID`为`2`字节`UUID`。
2. 如果等于`2`，表示描述符的`UUID`为`16`字节`UUID`。

因为该列表长度可能超过`PDU`要求的最大长度，所以需要多次执行请求和响应，直到服务端设备返回`未找到属性项`或到达结束句柄，才能获取全部的描述符：

<img src="./Generic Attribute Profile/查找全部符合条件的描述符.png" width=60%>

&emsp;&emsp;观察上图，服务端的响应数据第一个参数`0x01`表示`UUID1`和`UUID2`均为`2`字节`UUID`，第二个参数`0x0205`表示该描述符上级的特征值的句柄。

### 读取特征值

&emsp;&emsp;该功能包含`4`个子功能：

1. 读特征值。
2. 按`UUID`读特征值。
3. 读长包特征值。
4. 读多个特征值。

#### 读特征值

&emsp;&emsp;客户端已知特征值句柄，向服务端发送`Read Request`读取该句柄的特征值。
&emsp;&emsp;服务端返回指定句柄的特征值。该特征值长度应小于等于`ATT_MTU – 1`，如果大于该限制，则仅返回前`ATT_MTU – 1`个数据。下图为一次读取过程：

<img src="./Generic Attribute Profile/读取特征值.png" width=60%>

#### 按UUID读特征值

&emsp;&emsp;客户端已知特征值的`UUID`，不知道其句柄，向服务端发送`Read By Type Request`读取该特征值。具体操作与按`UUID`发现特征项一致。

#### 读长包特征值

&emsp;&emsp;客户端已知特征值的句柄，但是特征值的长度大于`ATT_MTU – 1`，向服务端发送`Read Request`以读取前`ATT_MTU – 1`个字节，然后发送`Read Blob Request`并设置合适的偏移量，以读取随后的`ATT_MTU – 1`个字节。重复执行`Read Blob Request`直到服务端的`Read Blob Response`内容小于`ATT_MTU – 1`，表明该特征值完全被读取。具体步骤如下：

<img src="./Generic Attribute Profile/读取长包特征值.png" width=60%>

#### 读多个特征值

&emsp;&emsp;客户端已知多个特征值的句柄，向服务端发送`Read Multiple Request`，参数为多个特征值句柄。服务端返回`Read Multiple Response`，包含了多个指定的特征值数据。

### 写特征值

&emsp;&emsp;该功能包含`5`个子功能：

1. 写命令。
2. 带签名的写命令。
3. 写请求。
4. 写长包请求。
5. 可靠的写请求。

#### 写命令

&emsp;&emsp;客户端已知特征值句柄，向服务端发送`Write Command`，写入指定数据。数据长度不能超过`ATT_MTU – 3`字节，如果超过，仅写入前`ATT_MTU – 3`个字节。该命令无需服务端返回响应。

#### 带签名的写命令

&emsp;&emsp;客户端已知特征值句柄，且链接没有经过认证，向服务端发送`Write Command`，并设置签名认证标志位，实现带签名的写命令。
数据长度不能超过`ATT_MTU – 3 – 12`字节，其中`12`表示认证签名的长度。如果超过，仅写入前`ATT_MTU – 3 – 12`个字节。该命令无需服务端返回响应。

#### 写请求

&emsp;&emsp;客户端已知特征值句柄，向服务端发送`Write Request`，写入指定数据。数据长度不能超过`ATT_MTU – 3`字节。如果超过，仅写入前`ATT_MTU – 3`个字节。该命令需要服务端返回响应`Write Response`。

#### 写长包请求

&emsp;&emsp;客户端已知特征值句柄，但待写入数据长度过长，向服务端发送`Prepare Write Request`，设置适当的偏移量，将数据发送至服务端缓存起来。数据发送完毕后，向服务端发送`Execute Write Request`执行写请求。
&emsp;&emsp;待写数据总长度不受限制，但是分步发送数据每次数据长度不得超过`ATT_MTU – 3`。两种请求均需要对应的服务端响应。
&emsp;&emsp;一个写长包请求流程如下：

<img src="./Generic Attribute Profile/读取长包请求.png" width=60%>

#### 可靠的写请求

&emsp;&emsp;客户端已知特征值句柄，希望一次性写入多字节的数据，或者要求数据的每个字节都必须被安全地写入服务端设备。向服务端发送`Prepare Write Request`，偏移量永远等于`0`，一次性只发送一个数据，待多字节数据缓存完毕，再发送`Execute Write Request`执行写请求。具体的操作与写长包请求完全一致。

### 通知

&emsp;&emsp;该功能包含一个子功能：通知。服务端执行`Handle Value Notification`，参数为特征值句柄和通知数据，向客户端推送通知。
&emsp;&emsp;执行通知前，该特征值需要已经使能通知，并且将通知数据写入该特征值。该命令无需客户端返回响应。

### 指示

&emsp;&emsp;该功能包含一个子功能：指示。服务端执行`Handle Value Indication`，参数为特征值句柄和指示数据，向客户端推送指示。
&emsp;&emsp;执行指示前，该特征值需要已经使能指示，并且将指示数据写入该特征值。该命令需要客户端返回响应`Handle Value Confirmation`。

### 读写描述符

&emsp;&emsp;该功能包含`4`个子功能：

1. 读描述符：读描述符与读特征值一致。
2. 读长包描述符：读长包描述符与读长包特征值一致。
3. 写描述符：写描述符与写请求一致。
4. 写长包描述符：写长包描述符与写长包请求一致。

### 与L2CAP层互操作

&emsp;&emsp;`GATT`使用的`L2CAP`固定信道传输属性数据。`GATT`的`PDU`长度限制`ATT_MTU`默认大小为`23`，它与`L2CAP`层的`MTU`值保持一致。