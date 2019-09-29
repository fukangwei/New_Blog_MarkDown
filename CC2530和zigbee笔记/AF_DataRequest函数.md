---
title: AF_DataRequest函数
categories: CC2530和zigbee笔记
abbrlink: d3d20d13
date: 2019-02-05 14:21:35
---
&emsp;&emsp;`Zigbee`协议栈进行数据发送是调用`AF_DataRequest`这个函数，该函数会调用协议栈里面与硬件相关的函数最终将数据通过天线发送出去。

``` cpp
afStatus_t AF_DataRequest (
    afAddrType_t *dstAddr, /* 目的地址指针 */
    endPointDesc_t *srcEP, /* 发送节点的端点描述符指针 */
    uint16 cID, /* ClusID 簇ID号 */
    uint16 len, /* 发送数据的长度 */
    uint8 *buf, /* 指向存放发送数据的缓冲区指针 */
    uint8 *transID, /* 传输序列号，该序列号随着信息的发送而增加 */
    uint8 options, /* 发送选项 */
    uint8 radius /* 最大传输半径(发送的跳数) */
);
```

&emsp;&emsp;`dstAddr`参数包含了目的节点的网络地址、端点号及数据传送的模式，如单播、广播或多播等。`afAddrType_t`是个结构体：

``` cpp
typedef struct {
    union {
        uint16 shortAddr; /* 用于标识该节点网络地址的变量 */
    } addr;

    afAddrMode_t addrMode; /* 用于指定数据传送模式，单播、多播还是广播 */
    byte endPoint; /* 指定的端口号，端口号241至254是保留端口 */
} afAddrType_t; /* 其定义在AF.h中 */
```

因为在`Zigbee`中，数据包可以单点传送(`unicast`)、多点传送(`multicast`)或者广播传送，所以必须有地址模式参数。一个单点传送数据包只发送给一个设备，多点传送数据包则要传送给一组设备，而广播数据包则要发送给整个网络的所有节点。因此上述结构体中的`addrMode`就是用于指定数据传送模式，它是个枚举类型，可以设置为以下几个值：

``` cpp
typedef enum {
    afAddrNotPresent = AddrNotPresent, /* 表示通过绑定关系指定目的地址 */
    afAddr16Bit = Addr16Bit, /* 单播发送 */
    afAddrGroup = AddrGroup, /* 组播 */
    afAddrBroadcast = AddrBroadcast /* 广播 */
} afAddrMode_t;
​
enum {
    AddrNotPresent = 0,
    AddrGroup = 1,
    Addr16Bit = 2,
    Addr64Bit = 3, /* 指定IEEE地址进行单播传输 */
    AddrBroadcast = 15
};
```

注意，`ZigBee`设备有两种类型的地址：一种是`64`位`IEEE`地址(物理)，即`MAC`地址；另一种是`16`位网络地址。`64`位地址使全球唯一的地址，设备将在它的生命周期中一直拥有它。它通常由制造商或者被安装时设置，这些地址由`IEEE`来维护和分配。`16`位网络地址是当设备加入网络后由协调器或路由器分配的，它在网络中是唯一的，用来在网络中鉴别设备和发送数据。
&emsp;&emsp;`srcEP`是发送节点的端点描述符指针，在`Zigbee`网络中，可以通过网络地址找到某个具体的节点，但是具体到某个节点，还有不同的端口(每个节点上最多可支持`240`个端口)，不同节点的端口间可以相互通信。例如节点`1`的端口`1`可以给节点`2`的控制端口`1`发`led`控制命令，也可以给节点`2`的端口`2`发采集命令。但是同一个节点上的端口的网络地址是相同的，所以仅仅通过网络地址无法区分节点`1`的端口`1`是与节点`2`的哪个端口进行通信，因此在发送数据时不但要指定网络地址，还要指点端口号。因此得出结论：使用网络地址来区分不同的节点，使用端口号区分同一节点上的端口。

``` cpp
typedef struct {
    byte endPoint; /* 端点号 */
    byte *task_id; /* 哪一个任务的端点号(调用任务的ID) */
    SimpleDescriptionFormat_t *simpleDesc; /* 描述一个Zigbee设备节点，称为简单设备描述符 */
    afNetworkLatencyReq_t latencyReq; /* 枚举结构，这个字段必须为nolatencyreqs */
} endPointDesc_t; /* 其定义在AF.h中 */
​
typedef struct {
    byte EndPoint; /* EP */
    uint16 AppProfId; /* 应用规范ID */
    uint16 AppDeviceId; /* 特定规范ID的设备类型 */
    byte AppDevVer: 4; /* 特定规范ID的设备的版本 */
    byte Reserved: 4; /* AF_V1_SUPPORT uses for AppFlags:4 */
    byte AppNumInClusters; /* 输入簇ID的个数 */
    cId_t *pAppInClusterList; /* 输入簇ID的列表 */
    byte AppNumOutClusters; /* 输出簇ID的个数 */
    cId_t *pAppOutClusterList; /* 输出簇ID的列表 */
} SimpleDescriptionFormat_t; /* 其定义在AF.h中 */
​
typedef enum {
    noLatencyReqs,
    fastBeacons,
    slowBeacons
} afNetworkLatencyReq_t;
```

- `cID`是簇`ID`号，一个`Zigbee`节点有很多属性，一个簇实际上是一些相关命令和属性的集合。在整个网络中，每个簇都有唯一的簇`ID`，也就是用来标识不同的控制操作。
- `len`是送数据的长度，`buf`指向发送数据缓冲的指针。
- `transID`该参数是指向发送序号的指针，每发送一个数据包，该发送序号会自动加1，因此在接收端可以查看接收数据包的序号来计算丢包率。
- `options`是发送选项，有如下选项：

``` cpp
#define AF_FRAGMENTED   0x01
/* 要求APS应答，这是应用层的应答，只在直接发送(单播)时使用 */
#define AF_ACK_REQUEST  0x10
/* 总要包含这个选项 */
#define AF_DISCV_ROUTE  0x20
#define AF_EN_SECURITY  0x40
/* 设置这个选项将导致设备跳过路由而直接发送消息。
   终点设备将不向其父亲发送消息。在直接发送(单播)和广播消息时很好用 */
#define AF_SKIP_ROUTING 0x80
```

- `radius`是最大的跳数，取默认值`AF_DEFAULT_RADIUS`。

&emsp;&emsp;返回值是`afStatus_t`类型，它是枚举型：

``` cpp
typedef enum {
    afStatus_SUCCESS,
    afStatus_FAILED = 0x80,
    afStatus_MEM_FAIL,
    afStatus_INVALID_PARAMETER
} afStatus_t;
```

&emsp;&emsp;下面是这个函数完整的源代码：

``` cpp
afStatus_t AF_DataRequest ( afAddrType_t *dstAddr, endPointDesc_t *srcEP,
                            uint16 cID, uint16 len, uint8 *buf, uint8 *transID,
                            uint8 options, uint8 radius ) {
    pDescCB pfnDescCB;
    ZStatus_t stat;
    APSDE_DataReq_t req;
    afDataReqMTU_t mtu;
​
    if ( srcEP == NULL ) { /* Verify source end point 判断源节点是否为空 */
        return afStatus_INVALID_PARAMETER;
    }
​
#if !defined( REFLECTOR )
​
    if ( dstAddr->addrMode == afAddrNotPresent ) {
        return afStatus_INVALID_PARAMETER;
    }
​
#endif
    /* Verify destination address 判断目的地址 */
    req.dstAddr.addr.shortAddr = dstAddr->addr.shortAddr;
​
    /* Validate broadcasting 判断地址的模式 */
    if ( ( dstAddr->addrMode == afAddr16Bit ) || ( dstAddr->addrMode == afAddrBroadcast ) ) {
        /* Check for valid broadcast values 核对有效的广播值 */
        if ( ADDR_NOT_BCAST != NLME_IsAddressBroadcast ( dstAddr->addr.shortAddr ) ) {
            /* Force mode to broadcast 强制转换成广播模式 */
            dstAddr->addrMode = afAddrBroadcast;
        } else {
            /* Address is not a valid broadcast type 地址不是一个有效的广播地址类型 */
            if ( dstAddr->addrMode == afAddrBroadcast ) {
                return afStatus_INVALID_PARAMETER;
            }
        }
    } else if ( dstAddr->addrMode != afAddrGroup && dstAddr->addrMode != afAddrNotPresent ) {
        return afStatus_INVALID_PARAMETER;
    }
​
    req.dstAddr.addrMode = dstAddr->addrMode;
    req.profileID = ZDO_PROFILE_ID;
​
    if ( ( pfnDescCB = afGetDescCB ( srcEP ) ) ) {
        uint16 *pID = ( uint16 * ) ( pfnDescCB ( AF_DESCRIPTOR_PROFILE_ID, srcEP->endPoint ) );
​
        if ( pID ) {
            req.profileID = *pID;
            osal_mem_free ( pID );
        }
    } else if ( srcEP->simpleDesc ) {
        req.profileID = srcEP->simpleDesc->AppProfId;
    }
​
    req.txOptions = 0;
​
    if ( ( options & AF_ACK_REQUEST ) &&
         ( req.dstAddr.addrMode != AddrBroadcast ) &&
         ( req.dstAddr.addrMode != AddrGroup ) ) {
        req.txOptions |= APS_TX_OPTIONS_ACK;
    }
​
    if ( options & AF_SKIP_ROUTING ) {
        req.txOptions |= APS_TX_OPTIONS_SKIP_ROUTING;
    }
​
    if ( options & AF_EN_SECURITY ) {
        req.txOptions |= APS_TX_OPTIONS_SECURITY_ENABLE;
        mtu.aps.secure = TRUE;
    } else {
        mtu.aps.secure = FALSE;
    }
​
    mtu.kvp = FALSE;
    req.transID = *transID;
    req.srcEP = srcEP->endPoint;
    req.dstEP = dstAddr->endPoint;
    req.clusterID = cID;
    req.asduLen = len;
    req.asdu = buf;
    req.discoverRoute = TRUE; // (uint8)((options & AF_DISCV_ROUTE) ? 1 : 0);
    req.radiusCounter = radius;
​
    if ( len > afDataReqMTU ( &mtu ) ) {
        if ( apsfSendFragmented ) {
            req.txOptions |= AF_FRAGMENTED | APS_TX_OPTIONS_ACK;
            stat = ( *apsfSendFragmented ) ( &req );
        } else {
            stat = afStatus_INVALID_PARAMETER;
        }
    } else {
        stat = APSDE_DataReq ( &req );
    }
​
    /*
     * If this is an EndPoint-to-EndPoint message on the same device, it will not
     * get added to the NWK databufs. So it will not Go OTA and it will not get
     * a MACCB_DATA_CONFIRM_CMD callback. Thus it is necessary to generate the
     * AF_DATA_CONFIRM_CMD here. Note that APSDE_DataConfirm() only generates one
     * message with the first in line TransSeqNumber, even on a multi message.
     * Also note that a reflected msg will not have its confirmation generated here.
     */
    if ( ( req.dstAddr.addrMode == Addr16Bit ) &&
         ( req.dstAddr.addr.shortAddr == NLME_GetShortAddr() ) ) {
        afDataConfirm ( srcEP->endPoint, *transID, stat );
    }
​
    if ( stat == afStatus_SUCCESS ) {
        ( *transID )++;
    }
​
    return ( afStatus_t ) stat;
}
```