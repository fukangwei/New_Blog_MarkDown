---
title: Service Discovery Protocol
date: 2020-12-09 05:44:59
categories: BLE笔记
---
&emsp;&emsp;`SDP`(服务发现协议)用于让`Client`应用程序发现`Server`应用程序提供的服务，以及这些服务的属性。<!--more-->
&emsp;&emsp;`SDP`提供了一套`SDP Server`和`SDP Client`之间的通讯方法。`SDP Server`维护着一个服务条目列表，这个列表存放着`SDP Server`对应的服务以及服务特性；每个服务条目对应一个单独的服务。`SDP Client`可以通过发送请求得到`SDP Server`维护的这个服务条目`List`。

&emsp;&emsp;如果`SDP Client`上的应用程序决定使用某个`Service`，则开启一个针对此服务的连接到服务提供者。`SDP`只是提供侦测`Service`以及特性的方法，不提供如何使用这些`Service`的方法。
&emsp;&emsp;每个`Bluetooth Device`最多有一个`SDP Server`。如果某个`Device`只作为`client`，则不需要拥有`SDP Server`。一个`Bluetooth Device`可以同时为`SDP Client`和`SDP Server`。
&emsp;&emsp;注意，当`SDP Server`添加或删除某个`Service`时，不会主动提供给`Client`。

### Service Record

&emsp;&emsp;`SDP Server`使用`Service Record`来维护关于一个`Servcie`的所有信息的集合。该`Service`的所有信息都是用`Service Attribute`来表示的。

&emsp;&emsp;而每一个`Service Attribute`的结构由下图所示：

&emsp;&emsp;其中，`Attribute ID`决定了`Assigned Value`的含义，例如`Record Handle Attribute`的`ID`为`0x0000`。
&emsp;&emsp;所以，我们可以把`Service Record`看成一个`Attribute List`。当`Client`来读取`SDP Server`时，实际上就是获取这个`Attribute List`中的信息。
&emsp;&emsp;在`SDP Server`中，用`Service Record Handle`来唯一地标识一个`Service Record`，而`Service Record Handle`则使用`32 bit`的数字来表示。在`Service Record List`中，`Server`使用`0x00000000`来表示`SDP`本身。当`SDP Client`和`SDP Server`建立后，一个`Service Record Handle`将一直有效，直到服务器删除了它。

### Service Class

&emsp;&emsp;`Service Class`是一种`Service`的类别，它提供了所有属于这个类别的`Attribute`的定义，它类似于面向对象编程中的类，具体的`Service`是类的实例；一个具体的`Service`可能隶属于多个类别，类似面向对象的里面的继承。当然，每个`Class`本身也是要和别的`Class`来做区分的，承担这项任务的变量叫`Service Class Identifier`。在`Service`中，总会有一个叫做`ServiceClassIDList的Attribute`，`Service Class Identifier`就存储在这个`Attribute`的`Attribute Value`里面，这个值是一个`UUID`。

### 服务搜索

&emsp;&emsp;如果客户端得到了`Service Record Handle`，就可以容易得到服务的内容。可以使用`SDP`服务搜索得到`Service Record Handle`。`SDP`只提供了基于`UUID`的`Service Record`搜索功能，它允许客户获取指定的`Service Record Handle`。

### 服务搜索模式

&emsp;&emsp;服务搜索模式(`Service Search Patterns`)使用`UUID`列表来定位匹配的`Service Record`。

### 服务浏览

&emsp;&emsp;`SDP`提供了基于`Service Class`的`Attribute`来浏览`Service`，这个`Attribute`被称为`BrowseGroupList`。`Client`通过创建一个包含代表`root browse group`的`UUID`的`Service Search Patterns`来浏览`Server`的`Service`。

### 数据表示

&emsp;&emsp;`SDP`使用`Data Element`来表示数据(`Attribute ID`、`Attribute ID range`和`Attribute value`)。
&emsp;&emsp;`Data Element`是一种类型化的数据表示，它由`2`个字段组成：

- 首部字段(`Header Field`)
- 数据字段(`Data Field`)

&emsp;&emsp;首部字段包含`2`个部分：

- 类型描述符(`Type Descriptor`)
- 大小描述符(`Size Descriptor`)

&emsp;&emsp;数据字段是一个字节序列，其长度由`Size Descriptor`指定，其含义则由`Type Descriptor`指定。

### Type Descriptor

&emsp;&emsp;`Data Element`的类型使用`5 bit`的`Type Descriptor`来表示，它包含在`Header Field`第一个字节的`高5位`。下面是已经定义的类型：

### Size Descriptor

&emsp;&emsp;`Data Element`的`Size Descriptor`包含在`Header Field`第一个字节的`低3位`：

### Data Element

&emsp;&emsp;`Data Element`的实例如下：

### 协议说明

&emsp;&emsp;`SDP`使用`Request/Response`模型，其中每个事务(`Transaction`)包含一个请求协议数据单元`PDU`和一个响应`PDU`。
&emsp;&emsp;`SDP`使用`L2CAP`作为传输协议，在建立连接并发出`SDP Request`后，在给定的时间内，只有收到该`Request`的`Response`后，才能发出其他的`Request`。
&emsp;&emsp;`SDP`的传输采用了`Big-Endian`，即高位先低位后的方式。
&emsp;&emsp;`SDP`的`PDU`包含一个`Header`和`Parameters`，而`Header`包含`3`个字段：`PDU ID`、`Transaction ID`和`Parameter Length`，如下图所示：

&emsp;&emsp;`PDU ID`如下图所示：

&emsp;&emsp;`Transaction ID`如下图所示：

&emsp;&emsp;`Parameter Length`如下图所示：