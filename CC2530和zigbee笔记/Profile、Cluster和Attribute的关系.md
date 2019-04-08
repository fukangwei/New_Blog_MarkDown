---
title: Profile、Cluster和Attribute的关系
date: 2019-02-05 14:05:14
tags:
---
&emsp;&emsp;`zigbee`规范引入了`profile`、`cluster`的概念。具体说来，假设规范一个`profile`(可以理解成一套规定)，这个`profile`用来规范智能家居领域的相关产品都要满足那些要求，那么`home automation public profile`就规定了智能家居都要做什么。当然你可以自己规范一个`profile`，称为`private profile`。而`zigbee`联盟则已经规范了一些`profile`，比如`home automation`、`smart energy`和`building automation`等。一个`public profile`也规定了`profile`的`ID`，比如智能家居就规定是`0x104`。协议栈本身也有一个`profile`，就是`Zigbee Device Profile`，也就是`ZDP`，这里规范了一个`zigbee`节点都要具备的功能，比如路由能力、网络发现能力、各个协议层需要做什么工作等。
&emsp;&emsp;在一个`profile`的规范下，又提出了`cluster`的概念。`cluster`要理解成一个大方向下的一个特定对象，比如智能家居下的一个调光器，操作这个调光器就需要一些命令，比如变亮、变暗、关灯、开灯这些操作。另外这个调光器也会有一个`attribute`(属性)，比如当前的亮度，由亮变暗的过程经历多长时间(一下子变亮视觉感觉没有渐变效果好)。`home automation`的`public profile`已经规定了调光器应该有哪些`cluster`，例如`Color Control Cluster`、`Ballast Configuration Cluster`等。然后，`profile`也规范了`color control cluster`的`ID`，这个就是`clusterID`了。
&emsp;&emsp;如果学过面向对象编程的思想，那就更好理解了：其实`profile`就相当于面向对象编程中的类，而`cluster`就是面向对象编程中的对象。至于`command`，你可以理解为每个类中的方法，而`attribute`则是每个对象的属性。比如你定义了一个智能家居的类(`profile = 0x104`)，那么是不是需要包括很多设备呀？比如具体的灯、开关什么的。所以在类的基础上需要去实例化一个对象调光器。这个调光器是不是需要一些方法呢？例如控制灯开关什么的，这个就相当于`command`。而每个设备对象本身都应该有一些自己的属性来描述这个设备，所以需要一个`attribute`。
&emsp;&emsp;在这个`cluster`下面，要有以下命令：

``` cpp
#define COMMAND_LIGHTING_MOVE_TO_HUE                0x00
#define COMMAND_LIGHTING_MOVE_HUE                   0x01
#define COMMAND_LIGHTING_STEP_HUE                   0x02
#define COMMAND_LIGHTING_MOVE_TO_SATURATION         0x03
#define COMMAND_LIGHTING_MOVE_SATURATION            0x04
#define COMMAND_LIGHTING_STEP_SATURATION            0x05
#define COMMAND_LIGHTING_MOVE_TO_HUE_AND_SATURATION 0x06
#define COMMAND_LIGHTING_MOVE_TO_COLOR              0x07
#define COMMAND_LIGHTING_MOVE_COLOR                 0x08
#define COMMAND_LIGHTING_STEP_COLOR                 0x09
#define COMMAND_LIGHTING_MOVE_TO_COLOR_TEMPERATURE  0x0a
```

`Ballast Configuration Cluster`下面则没有定义命令。
&emsp;&emsp;除了命令之外，每一个`cluster`还会定义一些属性，例如`colorcontrol cluster`下有：

``` cpp
#define ATTRID_LIGHTING_COLOR_CONTROL_CURRENT_HUE        0x0000
#define ATTRID_LIGHTING_COLOR_CONTROL_CURRENT_SATURATION 0x0001
#define ATTRID_LIGHTING_COLOR_CONTROL_REMAINING_TIME     0x0002
#define ATTRID_LIGHTING_COLOR_CONTROL_CURRENT_X          0x0003
#define ATTRID_LIGHTING_COLOR_CONTROL_CURRENT_Y          0x0004
#define ATTRID_LIGHTING_COLOR_CONTROL_DRIFT_COMPENSATION 0x0005
#define ATTRID_LIGHTING_COLOR_CONTROL_COMPENSATION_TEXT  0x0006
#define ATTRID_LIGHTING_COLOR_CONTROL_COLOR_TEMPERATURE  0x0007
#define ATTRID_LIGHTING_COLOR_CONTROL_COLOR_MODE         0x0008
```

而`Ballast Configuration Cluster`则有：

``` cpp
/* Ballast Information attribute set */
#define ATTRID_LIGHTING_BALLAST_CONFIG_PHYSICAL_MIN_LEVEL 0x0000
#define ATTRID_LIGHTING_BALLAST_CONFIG_PHYSICAL_MAX_LEVEL 0x0001
#define ATTRID_LIGHTING_BALLAST_BALLAST_STATUS            0x0002
```

这些属性反映了这个`cluster`下设备的状态，可以通过读写这些属性来改变其值。
&emsp;&emsp;总结说来，`Profile`规范了应该包括哪些`cluster`，一个`cluster`会有一个`ID`，而在一个`cluster`下又会有很多`command`，也会有很多`attibute`；在一个`cluster`下面，`command`和`attribute`的`ID`要唯一，不同的`cluster`下可以重复。在不同的`profile`下面，`clusterID`也可以重复。
&emsp;&emsp;再延伸一些，`zigbee`联盟在协议栈之外又增加了一部分操作cluster的函数，那就是`zigbee cluster library`(`ZCL`)，这里已经以源代码的形式提供了操作联盟规范的那些`public profile`下的函数，主要功能包括一些`command`的`transmit`、`response`、`indicate`以及`confirm`等，还有读写`attribute`的一些操作函数。所以在理解了`ZCL`的工作机制基础上，通过调用`ZCL`的函数实际上会让应用程序设计变得简单。
&emsp;&emsp;假设我们要控制一个`LED`，有一个远程节点(发命令控制`led`)和一个本地节点(接收命令并让`led`亮起来)，那么如果引入`ZCL`的概念，你可以设置操作`led`的事情是一个`cluster`，其下包含三个命令，即`open`、`close`和`read attribute`。灯还有一个`attribute`，那就是当前的`status`，远程节点可以用`ZCL`的函数发`open`和`close`命令，也可以随时发一个`read attibute`命令读取本地节点`led`的状态。这么做的好处是不需要再设计一个规定(比如一个数据包的第几个字节表示什么内容)，而是直接调用`ZCL`。这对于`command`和`attribute`数量很少的应用不见得有多大好处，但是当`command`和`attribute`数量很多的时候，引入`ZCL`会让事情变得简单。

---

&emsp;&emsp;`ZigBee`网络进行数据传输都是建立在应用规范(`profile`)的基础上。`profile`可以理解成一套规定，每个应用对应一个`profile ID`，每个`profile ID`可以应用于某项具体的应用，例如自动家居、楼宇自动化等。。
&emsp;&emsp;`ZigBee`联盟已经规定了`profile`的使用，整个应用规范可以分为公共规范(`Public profile`)和制造商规范(`Manufacturer Specific profile`)。公共规范的`ID`号为`0x0000`至`0x7FFF`，制造商的为`0xBF00`至`0xFFFF`。其中公共规范已经规定了常见的各种应用，以下是摘录的部分公共规范：

Profile ID | Profile Name
-----------|-------------
`0101`     | `Industial Plant Monitoring`(`IPM`)
`0104`     | `Home Automation`(`HA`)
`0105`     | `Commercial Building Automation`(`CBA`)
`0107`     | `Telecom Applications`(`TA`)
`0108`     | `Personal Home&hospital Care`(`PHHC`)
`0109`     | `Advanced Metering Initiative`(`AMI`)

&emsp;&emsp;`cluster`的代码如下：

``` cpp
#define SAMPLEAPP_MAX_CLUSTERS       2
#define SAMPLEAPP_PERIODIC_CLUSTERID 1
#define SAMPLEAPP_FLASH_CLUSTERID    2
​
/* This list should be filled with Application specific Cluster IDs */
const cId_t SampleApp_ClusterList[SAMPLEAPP_MAX_CLUSTERS] = {
    SAMPLEAPP_PERIODIC_CLUSTERID,
    SAMPLEAPP_FLASH_CLUSTERID
};
```

如何描述节点上一个具体的端口，这在规范中也有定义，使用简单描述符来描述一个端口：

``` cpp
/* These constants are only for example and should be changed to the device's needs */
#define SAMPLEAPP_ENDPOINT       20
#define SAMPLEAPP_PROFID         0x0F08
#define SAMPLEAPP_DEVICEID       0x0001
#define SAMPLEAPP_DEVICE_VERSION 0
#define SAMPLEAPP_FLAGS          0
​
const SimpleDescriptionFormat_t SampleApp_SimpleDesc = {
    SAMPLEAPP_ENDPOINT,                /* int Endpoint; */
    SAMPLEAPP_PROFID,                  /* uint16 AppProfId[2]; */
    SAMPLEAPP_DEVICEID,                /* uint16 AppDeviceId[2]; */
    SAMPLEAPP_DEVICE_VERSION,          /* int AppDevVer:4; */
    SAMPLEAPP_FLAGS,                   /* int AppFlags:4; */
    SAMPLEAPP_MAX_CLUSTERS,            /* uint8 AppNumInClusters; */
    ( cId_t * ) SampleApp_ClusterList, /* uint8 *pAppInClusterList; */
    SAMPLEAPP_MAX_CLUSTERS,            /* uint8 AppNumInClusters; */
    ( cId_t * ) SampleApp_ClusterList  /* uint8 *pAppInClusterList; */
};
```

这就描述了一个节点的端口`20`。