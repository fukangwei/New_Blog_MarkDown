---
title: uip的ICMP
categories: Contiki和uip
date: 2019-02-04 22:48:47
---
&emsp;&emsp;下面是`IP`和`ICMP`报文的结构体：<!--more-->

``` cpp
typedef struct name { /* The ICMP and IP headers */
    /* IP header */
    u8_t vhl, /* 4位版本标识，有ipv4和ipv6两个版本 */
         tos, /* 8位区分服务 */
         len[2], /* IP首部和数据部分的总长度 */
         ipid[2], /* 分片报文用来认识自己同胞的 */
         ipoffset[2], /* 片偏移，分片报文用，其中前3位为分片标识 */
         ttl, /* 生存时间 */
         proto; /* 上层协议，ICMP的话应该填1 */
    u16_t ipchksum; /* 校验和 */
    u16_t srcipaddr[2], /* 源IP */
          destipaffr[2]; /* 目的IP */
    /* ICMP(echo) header */
    u8_t type, /* ICMP报文，request请求类型填8，relay应答类型填0 */
         icode; /* 这里填0即可 */
    u16_t icmpchksum; /* 包括数据在内整个ICMP数据报的检验和 */
    /* 下面两个字段是ICMP请求应答中特有的 */
    u16_t id, /* 标识符，标识本ICMP进程 */
          seqno; /* 对request的应答reply要和request有相同的序列号 */
} uip_icmpip_hdr;
```

&emsp;&emsp;`uip`只对`ICMP request(echo)`进行响应，其他`ICMP`报文全部丢弃：

``` cpp
/* ICMP echo (i.e., ping) processing. This is simple,
   we only change the ICMP type from ECHO to ECHO_REPLY
   and adjust the ICMP checksum before we return the packet. */
if ( ICMPBUF->type != ICMP_ECHO ) {
    UIP_STAT ( ++uip_stat.icmp.drop );
    UIP_STAT ( ++uip_stat.icmp.typeerr );
    UIP_LOG ( "icmp: not icmp echo." );
    goto drop;
}
```

然后做三件事：把`ICMP`报文类型改成`ICMP`应答、修改`ICMP`校验和、互换目的`IP`和源`IP`，就可以发送出去了：

``` cpp
ICMPBUF->type = ICMP_ECHO_REPLY; /* 把报文改成ICMP应答(reply) */

/* 修改校验和 */
if ( ICMPBUF->icmpchksum >= HTONS ( 0xffff - ( ICMP_ECHO << 8 ) ) ) {
    ICMPBUF->icmpchksum += HTONS ( ICMP_ECHO << 8 ) + 1;
} else {
    ICMPBUF->icmpchksum += HTONS ( ICMP_ECHO << 8 );
}

/* Swap IP addresses. */
tmp16 = BUF->destipaddr[0];
BUF->destipaddr[0] = BUF->srcipaddr[0];
BUF->srcipaddr[0] = tmp16;
tmp16 = BUF->destipaddr[1];
BUF->destipaddr[1] = BUF->srcipaddr[1];
BUF->srcipaddr[1] = tmp16;

UIP_STAT ( ++uip_stat.icmp.sent );
goto send;
```


---

&emsp;&emsp;`ICMP`是`Internet`控制报文协议，是一种面向无连接的协议。它是`TCP/IP`协议族的一个子协议，用于在`IP`主机、路由器之间传递控制消息。控制消息是指网络通不通、主机是否可达、路由是否可用等网络本身的消息。这些控制消息虽然并不传输用户数据，但是对于用户数据的传递起着重要的作用。
&emsp;&emsp;`ICMP`提供一种出错报告信息。发送的出错报文返回到发送原数据的设备，因为只有发送设备才是出错报文的逻辑接受者。发送设备随后可根据`ICMP`报文确定发生错误的类型，并确定如何才能更好地重发失败的数据包。但是`ICMP`唯一的功能是报告问题而不是纠正错误，纠正错误的任务由发送方完成。
&emsp;&emsp;在调试网络时经常会使用到`ICMP`协议，比如我们经常使用的用于检查网络通不通的`Ping`命令，这个`Ping`的过程实际上就是`ICMP`协议工作的过程。
&emsp;&emsp;实现`ICMP`网络控制报文协议时，只实现`echo`(`回响`)服务。`uIP`在生成回响报文时并不重新分配存储器空间，而是直接修改`echo`请求报文来生成回响报文。将`ICMP`类型字段从`echo`类型改变成`echo reply`类型，重新计算校验和，并修改校验和字段。

``` cpp
#define ICMPBUF ((struct uip_icmpip_hdr *)&uip_buf[UIP_LLH_LEN])
```