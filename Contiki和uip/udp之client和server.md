---
title: udp之client和server
date: 2019-02-05 09:47:51
categories: Contiki和uip
---
&emsp;&emsp;对于`udp-client.c`进行分析：

``` cpp
PROCESS_THREAD ( udp_client_process, ev, data ) {
    static struct etimer et;
    uip_ipaddr_t ipaddr;
    PROCESS_BEGIN();
    PRINTF ( "UDP client process started\n" );
#if UIP_CONF_ROUTER
    set_global_address();
#endif
    print_local_addresses(); /* 打印所有本地可用的地址 */
    static resolv_status_t status = RESOLV_STATUS_UNCACHED;
​
    while ( status != RESOLV_STATUS_CACHED ) {
        status = set_connection_address ( &ipaddr ); /* 设定连接的远端IP地址 */
​
        if ( status == RESOLV_STATUS_RESOLVING ) { /* 处理一些异常情况 */
            PROCESS_WAIT_EVENT_UNTIL ( ev == resolv_event_found );
        } else if ( status != RESOLV_STATUS_CACHED ) {
            PRINTF ( "Can't get connection address.\n" );
            PROCESS_YIELD();
        }
    }
​
    /* 这里和普通的socket编程一样，先申请一个conn，再进行绑定，其中conn中包括远端的IP地址 */
    client_conn = udp_new ( &ipaddr, UIP_HTONS ( 3000 ), NULL );
    udp_bind ( client_conn, UIP_HTONS ( 3001 ) );
    PRINTF ( "Created a connection with the server " );
    PRINT6ADDR ( &client_conn->ripaddr ); /* 将一个IPV6地址打印出来 */
    PRINTF ( "local/remote port %u/%u\n", UIP_HTONS ( client_conn->lport ), \
             UIP_HTONS ( client_conn->rport ) );
    etimer_set ( &et, SEND_INTERVAL );
​
    while ( 1 ) {
        PROCESS_YIELD();
​
        if ( etimer_expired ( &et ) ) {
            timeout_handler();
            etimer_restart ( &et );
        } else if ( ev == tcpip_event ) { /* 收到响应信息 */
            tcpip_handler();
        }
    }
​
    PROCESS_END();
}
```

我们再对其中的几个函数进行分析：

``` cpp
static void print_local_addresses ( void ) {
    int i;
    uint8_t state;
    PRINTF ( "Client IPv6 addresses: " );
​
    for ( i = 0; i < UIP_DS6_ADDR_NB; i++ ) {
        state = uip_ds6_if.addr_list[i].state;
​
        if ( uip_ds6_if.addr_list[i].isused && \
             ( state == ADDR_TENTATIVE || state == ADDR_PREFERRED ) ) {
            PRINT6ADDR ( &uip_ds6_if.addr_list[i].ipaddr );
            PRINTF ( "\n" );
        }
    }
}
```

这个函数的主要作用就是打印一下可用的`IP`地址。`UIP_DS6_ADDR_NB`这个应该是`IPV6`的地址的数量，在原来的代码中为`3`。`uip_ds6_if`是`uip-ds6`中定义的一个数据结构，包含了所有的接口变量：

``` cpp
typedef struct uip_ds6_netif {
    uint32_t link_mtu;
    uint8_t cur_hop_limit;
    uint32_t base_reachable_time; /* in msec */
    uint32_t reachable_time;      /* in msec */
    uint32_t retrans_timer;       /* in msec */
    uint8_t maxdadns;
    uip_ds6_addr_t addr_list[UIP_DS6_ADDR_NB];
    uip_ds6_aaddr_t aaddr_list[UIP_DS6_AADDR_NB];
    uip_ds6_maddr_t maddr_list[UIP_DS6_MADDR_NB];
} uip_ds6_netif_t;
```

`addr_list`中就是所有的`IPV6`地址。`for`循环就是将其中的`IPV6`地址都查找一遍，看它的状态。`IPV6`的地址有三种状态，一种是`PREFERRED`，代表`IPv6`地址可用；第二种是`TENTATIVE`，代表未知；最后一种代表其他主机正在使用，即`DEPRECATED`。

``` cpp
static void set_connection_address ( uip_ipaddr_t *ipaddr ) {
#define _QUOTEME(x) #x
#define QUOTEME(x) _QUOTEME(x)
#ifdef UDP_CONNECTION_ADDR
​
    if ( uiplib_ipaddrconv ( QUOTEME ( UDP_CONNECTION_ADDR ), ipaddr ) == 0 ) {
        PRINTF ( "UDP client failed to parse address '%s'\n", QUOTEME ( UDP_CONNECTION_ADDR ) );
    }
​
#elif UIP_CONF_ROUTER
    uip_ip6addr ( ipaddr, 0xaaaa, 0, 0, 0, 0x0212, 0x7404, 0x0004, 0x0404 );
#else
    uip_ip6addr ( ipaddr, 0xfe80, 0, 0, 0, 0x6466, 0x6666, 0x6666, 0x6666 );
#endif /* UDP_CONNECTION_ADDR */
}
```

这段代码就是将`IPV6`地址赋值到`ipaddr`当中。首先定义`UDP_CONNECTION_ADDR`变量，这个变量的作用就是放置文本型的`IPV6`地址。为了方便，需要将文本型的(也就是直接给出的那种`128`位地址)转换成通用的格式。`if`内是转换不成功的情况，会针对不成功的情况进行处理。
&emsp;&emsp;下面是`PROCESS_THREAD`的代码：

``` cpp
PROCESS_THREAD ( udp_server_process, ev, data ) {
#if UIP_CONF_ROUTER
    uip_ipaddr_t ipaddr;
#endif /* UIP_CONF_ROUTER */
    PROCESS_BEGIN();
    PRINTF ( "UDP server started\n" );
#if RESOLV_CONF_SUPPORTS_MDNS
    resolv_set_hostname ( "contiki-udp-server" ); /* 设置主机的名字 */
#endif
#if UIP_CONF_ROUTER
    uip_ip6addr ( &ipaddr, 0xaaaa, 0, 0, 0, 0, 0, 0, 0 ); /* 设置IPV6地址中后面部分的 */
    uip_ds6_set_addr_iid ( &ipaddr, &uip_lladdr ); /* 设置IPV6地址中的Initializer部分 */
    uip_ds6_addr_add ( &ipaddr, 0, ADDR_AUTOCONF ); /* 为地址的后面部分添加前缀 */
#endif /* UIP_CONF_ROUTER */
    print_local_addresses();
    server_conn = udp_new ( NULL, UIP_HTONS ( 3001 ), NULL );
    udp_bind ( server_conn, UIP_HTONS ( 3000 ) );
​
    while ( 1 ) {
        PROCESS_YIELD();
​
        if ( ev == tcpip_event ) {
            tcpip_handler();
        }
    }
​
    PROCESS_END();
}
```

`uip_ds6_set_addr_iid`函数如下：

``` cpp
void uip_ds6_set_addr_iid ( uip_ipaddr_t *ipaddr, uip_lladdr_t *lladdr ) {
    /* We consider only links with IEEE EUI-64 identifier or
     * IEEE 48-bit MAC addresses */
#if (UIP_LLADDR_LEN == 8)
    memcpy ( ipaddr->u8 + 8, lladdr, UIP_LLADDR_LEN );
    ipaddr->u8[8] ^= 0x02;
#elif (UIP_LLADDR_LEN == 6)
    memcpy ( ipaddr->u8 + 8, lladdr, 3 );
    ipaddr->u8[11] = 0xff;
    ipaddr->u8[12] = 0xfe;
    memcpy ( ipaddr->u8 + 13, ( uint8_t * ) lladdr + 3, 3 );
    ipaddr->u8[8] ^= 0x02;
#else
    #error uip-ds6.c cannot build interface address when UIP_LLADDR_LEN is not 6 or 8
#endif
}
```

这个函数就是为了给`IPv6`地址的`identifier`赋值。`IPv6`地址分为两部分，但是共享一段内存，对两个的任意一个赋值都可以：

``` cpp
typedef union uip_ip6addr_t {
    uint8_t  u8[16]; /* Initializer, must come first. */
    uint16_t u16[8];
} uip_ip6addr_t;
```

当我们不用`MAC`地址来赋值时，就需要用`IEEE 802.15.4`的地址来赋值。

``` cpp
uip_ds6_addr_add ( &ipaddr, 0, ADDR_AUTOCONF );
```

这个函数主要是给标识符赋值前`64`位。处理数据到来时的函数如下：

``` cpp
static void tcpip_handler ( void ) {
    static int seq_id;
    char buf[MAX_PAYLOAD_LEN];
​
    if ( uip_newdata() ) {
        ( ( char * ) uip_appdata ) [uip_datalen()] = 0;
        PRINTF ( "Server received: '%s' from ", ( char * ) uip_appdata );
        PRINT6ADDR ( &UIP_IP_BUF->srcipaddr );
        PRINTF ( "\n" );
        uip_ipaddr_copy ( &server_conn->ripaddr, &UIP_IP_BUF->srcipaddr );
        PRINTF ( "Responding with message: " );
        sprintf ( buf, "Hello from the server! (%d)", ++seq_id );
        PRINTF ( "%s\n", buf );
        uip_udp_packet_send ( server_conn, buf, strlen ( buf ) );
        /* Restore server connection to allow data from any node */
        memset ( &server_conn->ripaddr, 0, sizeof ( server_conn->ripaddr ) );
    }
}
```

查看库函数就可以很清楚的知道，`uip_newdata`就是代表是否有新的数据到来，`uip_appdata`直接就是代表到来的数据，`uip_datalen`是指到来数据的长度。

``` cpp
uip_ipaddr_copy ( &server_conn->ripaddr, &UIP_IP_BUF->srcipaddr );
```

这句代码是将客户端的地址赋值到申请的`conn`中，因为服务器需要回复客户机，因此需要客户机的地址。通过接收到的这个数据找到源端的地址。

``` cpp
#define UIP_IP_BUF ((struct uip_ip_hdr *)&uip_buf[UIP_LLH_LEN])
```

这句可以知道`UIP_IP_BUF`其实代表的是`uip_buf`，查看库函数可以知道`uip_buf`就是代表的接收数据的缓存区。但是这个数据不能直接将源端的地址提取出来，因此将其强制转换成`uip_ip_hdr`格式，而这种格式可以直接得到源端数据。`uip_ip_hdr`数据格式为：

``` cpp
struct uip_ip_hdr {
#if UIP_CONF_IPV6
    /* IPV6 header */
    uint8_t vtc;
    uint8_t tcflow;
    uint16_t flow;
    uint8_t len[2];
    uint8_t proto, ttl;
    uip_ip6addr_t srcipaddr, destipaddr;
#else /* UIP_CONF_IPV6 */
    /* IPV4 header */
    uint8_t vhl,
            tos,
            len[2],
            ipid[2],
            ipoffset[2],
            ttl,
            proto;
    uint16_t ipchksum;
    uip_ipaddr_t srcipaddr, destipaddr;
#endif /* UIP_CONF_IPV6 */
};
```

可以看到`srcipaddr`就是源端的地址。