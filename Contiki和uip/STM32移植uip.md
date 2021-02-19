---
title: STM32移植uip
categories: Contiki和uip
date: 2019-02-05 12:31:16
---
&emsp;&emsp;`uIP`由瑞典计算机科学学院的`Adam Dunkels`(`http://dunkels.com/adam/uip/`)开发，其源代码由`C`语言编写，并完全公开。有了这个`TCP/IP`协议栈，让嵌入式可以实现的功能更为丰富。可以作为`WebClient`向指定网站提交数据，也可以作为`WebServer`作为网页服务器，提供一个小型的动态页面访问功能。由于是开源的免费协议栈，据说`Uip`没有考虑协议安全的问题。<!--more-->
&emsp;&emsp;首先介绍下移植的环境：`stm32`开发板和`ENC28J60`网络模块。`ENC28J60`是带`SPI`接口的独立以太网控制器，可以用`mcu`控制`spi`来实现`tcp/ip`数据流的收发，所以要先完成`ENC28J60`的驱动程序，再整合`Uip`。`Uip`是用标准的`C`语言实现，所以移植`Uip`在`51`单片机和`stm32`上类似。
&emsp;&emsp;经过几天的琢磨，已经将`Uip`的几个示例稳定运行。`Uip`中`apps`下的例子相互之间存在冲突，源程序中也有一些`Error`要修改，我将`Uip`的文件结构做了一些调整。

### Uip文件结构

&emsp;&emsp;先介绍一下`Uip`各个目录文件的功能：

``` cpp
├- apps  # apps目录下为uip提供的一些应用示例
│├- dhcpc
│├- hello-world
│├- resolv
│├- smtp
│├- telnetd
│├- webclient
│└- webserver
│       └-httpd-fs
├- doc  # doc下放置的为说明文档，程序中用不上
│   └-html
├- lib  # lib下为内存块管理函数源码
├- uip  # uip下为uip和核心实现源码
└- unix  # unix环境里的uip应用例子，可以参照这个例子实现应用
```

### Uip移植

&emsp;&emsp;`Uip`的移植可以参考`uip`的`unix`的文件结构。`Uip`的数据通过网卡`Enc28j60`从物理层剥离，所以需要先配置`Uip`和`Enc28j60`的数据交互。这个部分在`tapdev.c`：

``` cpp
#include "uip.h"
#include "ENC28J60.h"

void tapdev_init ( unsigned char *my_mac ) {
    enc28j60Init ( my_mac );
}

unsigned int tapdev_read ( void ) {
    return enc28j60PacketReceive ( UIP_CONF_BUFFER_SIZE, uip_buf );
}

void tapdev_send ( void ) {
    enc28j60PacketSend ( uip_len, uip_buf );
}
```

网卡驱动程序与具体硬件相关，这一步比较费点时间，不过好在大部分网卡芯片的驱动程序都有代码借鉴或移植。驱动需要提供三个函数，以`Enc28j60`驱动为例：

- `tapdev_init`：网卡初始化函数，初始化网卡的工作模式。
- `tapdev_read`：读包函数。将网卡收到的数据放入全局缓存区`uip_buf`中，返回包的长度，赋给`uip_len`。
- `tapdev_send`：发包函数。将全局缓存区`uip_buf`里的数据(长度放在`uip_len`中)发送出去。

&emsp;&emsp;由于`uIP`协议栈需要使用时钟，为`TCP`和`ARP`的定时器服务。因此使用单片机的定时器或是`stm32`的滴答定时器用作时钟，每`20ms`让计数`tick_cnt`加`1`。这样，`25`次计数(`0.5s`)满了后可以调用`TCP`的定时处理程序，`10s`后可以调用`ARP`老化程序。`uIP1.0`版本增加了`timer.c`和`timer.h`，专门用来管理时钟。修改`clock-arch.c`：

``` cpp
#include "clock-arch.h"
#include "stm32f10x.h"

extern __IO int32_t g_RunTime;

clock_time_t clock_time ( void ) {
    return g_RunTime;
}
```

使用`stm32`滴答定时器中断代码(`User/stm32f10x_it.c`)：

``` cpp
__IO int32_t g_RunTime = 0;

void SysTick_Handler ( void ) {
    static uint8_t s_count = 0;

    if ( ++s_count >= 10 ) {
        s_count = 0;
        g_RunTime++; /* 全局运行时间每10ms增1 */

        if ( g_RunTime == 0x80000000 ) {
            g_RunTime = 0;
        }
    }
}
```

&emsp;&emsp;`uipopt.h`和`uip-conf.h`是配置文件，用来设置本地的`IP`地址、网关地址、`MAC`地址、全局缓冲区的大小、支持的最大连接数、侦听数、`ARP`表大小等，可以根据需要进行配置。

- `#define UIP_FIXEDADDR 1`：决定`uIP`是否使用一个固定的`IP`地址。如果`uIP`使用一个固定的`IP`地址，应该置位(`set`)这些`uipopt.h`中的选项。如果不的话，则应该使用宏`uip_sethostaddr`、`uip_setdraddr`和`uip_setnetmask`。
- `#define UIP_PINGADDRCONF 0`：`Ping`的`IP`地址赋值。
- `#define UIP_FIXEDETHADDR 0`：指明`uIP`的`ARP`模块是否在编译时使用一个固定的以太网`MAC`地址。
- `#define UIP_TTL 255`：`uIP`发送的`IP packets`的`IP`的`TTL`(`time to live`)。
- `#define UIP_REASSEMBLY 0`：`uIP`支持`IP packets`的分片和重组。
- `#define UIP_REASS_MAXAGE 40`：一个`IP fragment`在被丢弃之前可以在重组缓冲区中存在的最大时间。
- `#define UIP_UDP 0`：是否编译`UDP`。
- `#define UIP_ACTIVE_OPEN 1`：决定是否支持`uIP`打开一个连接。
- `#define UIP_CONNS 10`：同时可以打开的`TCP`连接的最大数目。由于`TCP`连接是静态分配的，减小这个数目将占用更少的`RAM`。每一个`TCP`连接需要大约`30`字节的内存。
- `#define UIP_LISTENPORTS 10`：同时监听的`TCP`端口的最大数目，每一个`TCP`监听端口需要`2`个字节的内存。
- `#define UIP_RECEIVE_WINDOW 32768`：建议的接收窗口的大小。如果应用程序处理到来的数据比较慢，那么应该设置的小一点(相对于`uip_buf`缓冲区的大小来说)。相反，如果应用程序处理数据很快，可以设置的大一点(`32768`字节)。
- `#define UIP_URGDATA 1`：决定是否支持`TCP urgent data notification`。
- `#define UIP_RTO 3`：`The initial retransmission timeout counted in timer pulses`，不要改变。
- `#define UIP_MAXRTX 8`：在中止连接之前，应该重发一个段的最大次数，不要改变。
- `#define UIP_TCP_MSS (UIP_BUFSIZE - UIP_LLH_LEN - 40)`：`TCP`段的最大长度。它不能大于`UIP_BUFSIZE - UIP_LLH_LEN - 40`。
- `#define UIP_TIME_WAIT_TIMEOUT 120`：一个连接应该在`TIME_WAIT`状态等待多长，不要改变。
- `#define UIP_ARPTAB_SIZE 8`：`ARP`表的大小。如果本地网络中有许多到这个`uIP`节点的连接，那么这个选项应该设置为一个比较大的值。
- `#define UIP_BUFSIZE 1500`：`uIP packet`缓冲区不能小于`60`字节，但也不能大于`1500`字节。
- `#define UIP_STATISTICS 1`：决定是否支持统计数字。统计数字对调试很有帮助，并展示给用户。
- `#define UIP_LOGGING 0`：输出`uIP`登陆信息。
- `#define UIP_LLH_LEN 14`：链接层头部长度。对于`SLIP`，应该设置成`0`。

&emsp;&emsp;`uip-conf.h`中增加几个主要结构体定义，不`include`任何应用：

``` cpp
#define UIP_CONF_LOGGING 0 /* logging off */

typedef int uip_tcp_appstate_t; /* 出错可注释 */
typedef int uip_udp_appstate_t; /* 出错可注释 */

/* #include "smtp.h" */
/* #include "hello-world.h" */
/* #include "telnetd.h" */
/* #include "webserver.h" */
/* #include "dhcpc.h" */
/* #include "resolv.h" */
/* #include "webclient.h" */

#include "app_call.h" /* 加入一个Uip的数据接口文件 */
```

&emsp;&emsp;`uIP`在接受到底层传来的数据包后，调用`UIP_APPCALL`，将数据送到上层应用程序处理(`User/app_call.c`)：

``` cpp
#include "stm32f10x.h"

#ifndef UIP_APPCALL
    #define UIP_APPCALL Uip_Appcall
#endif

#ifndef UIP_UDP_APPCALL
    #define UIP_UDP_APPCALL Udp_Appcall
#endif

void Uip_Appcall ( void );
void Udp_Appcall ( void );

void Uip_Appcall ( void ) {
    /* User Code */
}

void Udp_Appcall ( void ) {
    /* User Code */
}
```

&emsp;&emsp;加入`uIP`的的主循环代码架构(`User/main.c`)：

``` cpp
#include "stm32f10x.h"
#include "stdio.h"
#include "string.h"
#include "uip.h"
#include "uip_arp.h"
#include "tapdev.h"
#include "timer.h"
#include "ENC28J60.h"
#include "SPI.h"

#define PRINTF_ON 1
#define BUF ((struct uip_eth_hdr *)&uip_buf[0])

#ifndef NULL
    #define NULL (void *)0
#endif /* NULL */

static unsigned char mymac[6] = {0x04, 0x02, 0x35, 0x00, 0x00, 0x01};

void RCC_Configuration ( void );
void GPIO_Configuration ( void );
void USART_Configuration ( void );

int main ( void ) {
    int i;
    uip_ipaddr_t ipaddr;
    struct timer periodic_timer, arp_timer;
    RCC_Configuration();
    GPIO_Configuration();
    USART_Configuration();
    SPInet_Init();
    timer_set ( &periodic_timer, CLOCK_SECOND / 2 );
    timer_set ( &arp_timer, CLOCK_SECOND * 10 );
    SysTick_Config ( 72000 ); /* 配置滴答计时器 */
    tapdev_init ( mymac ); /* 以太网控制器驱动初始化 */
    uip_init(); /* Uip协议栈初始化 */
    uip_ipaddr ( ipaddr, 192, 168, 1, 15 ); /* 配置Ip */
    uip_sethostaddr ( ipaddr );
    uip_ipaddr ( ipaddr, 192, 168, 1, 1 ); /* 配置网关 */
    uip_setdraddr ( ipaddr );
    uip_ipaddr ( ipaddr, 255, 255, 255, 0 ); /* 配置子网掩码 */
    uip_setnetmask ( ipaddr );

    while ( 1 ) {
        uip_len = tapdev_read(); /* 从网卡读取数据 */

        if ( uip_len > 0 ) {
            /* 如果数据存在则按协议处理 */
            if ( BUF->type == htons ( UIP_ETHTYPE_IP ) ) {
                /* 如果收到的是IP数据，调用uip_input处理 */
                uip_arp_ipin();
                uip_input();

                /* If the above function invocation resulted in data that should be sent
                   out on the network, the global variable uip_len is set to a value > 0 */

                if ( uip_len > 0 ) {
                    uip_arp_out();
                    tapdev_send();
                }
            } else if ( BUF->type == htons ( UIP_ETHTYPE_ARP ) ) {
                /* 如果收到的是ARP数据，调用uip_arp_arpin处理 */
                uip_arp_arpin();

                /* If the above function invocation resulted in data that should be sent
                   out on the network, the global variable uip_len is set to a value > 0 */

                if ( uip_len > 0 ) {
                    tapdev_send();
                }
            }
        } else if ( timer_expired ( &periodic_timer ) ) {
            /* 查看0.5s是否到了，调用uip_periodic处理TCP超时程序 */
            timer_reset ( &periodic_timer );

            for ( i = 0; i < UIP_CONNS; i++ ) {
                uip_periodic ( i );

                /* If the above function invocation resulted in data that should be sent
                   out on the network, the global variable uip_len is set to a value > 0 */

                if ( uip_len > 0 ) {
                    uip_arp_out();
                    tapdev_send();
                }
            }

            for ( i = 0; i < UIP_UDP_CONNS; i++ ) {
                uip_udp_periodic ( i ); /* 处理udp超时程序 */

                /* If the above function invocation resulted in data that should be sent
                   out on the network, the global variable uip_len is set to a value > 0 */
                if ( uip_len > 0 ) {
                    uip_arp_out();
                    tapdev_send();
                }
            }

            /* Call the ARP timer function every 10 seconds -- 10s到了就处理ARP */
            if ( timer_expired ( &arp_timer ) ) {
                timer_reset ( &arp_timer );
                uip_arp_timer();
            }
        }
    }
}

void GPIO_Configuration ( void ) {
    GPIO_InitTypeDef GPIO_InitStructure;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
}

void RCC_Configuration ( void ) {
    ErrorStatus HSEStartUpStatus; /* 定义枚举类型变量HSEStartUpStatus */
    RCC_DeInit(); /* 复位系统时钟设置 */
    RCC_HSEConfig ( RCC_HSE_ON ); /* 开启HSE */
    HSEStartUpStatus = RCC_WaitForHSEStartUp(); /* 等待HSE起振并稳定 */

    if ( HSEStartUpStatus == SUCCESS ) { /* 判断HSE起是否振成功，是则进入if内部 */
        RCC_HCLKConfig ( RCC_SYSCLK_Div1 ); /* 选择HCLK(AHB)时钟源为SYSCLK，分频系数1分频 */
        RCC_PCLK2Config ( RCC_HCLK_Div1 ); /* 选择PCLK2时钟源为HCLK(AHB)，分频系数1分频 */
        RCC_PCLK1Config ( RCC_HCLK_Div2 ); /* 选择PCLK1时钟源为HCLK(AHB)，分频系数2分频 */
        FLASH_SetLatency ( FLASH_Latency_2 ); /* 设置FLASH延时周期数为2 */
        FLASH_PrefetchBufferCmd ( FLASH_PrefetchBuffer_Enable ); /* 使能FLASH预取缓存 */
        /* 选择锁相环(PLL)时钟源为HSE，分频系数1分频，倍频数为9，则PLL输出频率为“8MHz * 9 = 72MHz” */
        RCC_PLLConfig ( RCC_PLLSource_HSE_Div1, RCC_PLLMul_9 );
        RCC_PLLCmd ( ENABLE ); /* 使能PLL */

        while ( RCC_GetFlagStatus ( RCC_FLAG_PLLRDY ) == RESET ); /* 等待PLL输出稳定 */

        RCC_SYSCLKConfig ( RCC_SYSCLKSource_PLLCLK ); /* 选择SYSCLK时钟源为PLL */

        while ( RCC_GetSYSCLKSource() != 0x08 ); /* 等待PLL成为SYSCLK时钟源 */
    }

    /* 打开APB2总线上的GPIOA时钟 */
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA | RCC_APB2Periph_USART1, ENABLE );
}

void USART_Configuration ( void ) {
    USART_InitTypeDef USART_InitStructure;
    USART_ClockInitTypeDef USART_ClockInitStructure;
    USART_ClockInitStructure.USART_Clock = USART_Clock_Disable;
    USART_ClockInitStructure.USART_CPOL = USART_CPOL_Low;
    USART_ClockInitStructure.USART_CPHA = USART_CPHA_2Edge;
    USART_ClockInitStructure.USART_LastBit = USART_LastBit_Disable;
    USART_ClockInit ( USART1, &USART_ClockInitStructure );
    USART_InitStructure.USART_BaudRate = 9600;
    USART_InitStructure.USART_WordLength = USART_WordLength_8b;
    USART_InitStructure.USART_StopBits = USART_StopBits_1;
    USART_InitStructure.USART_Parity = USART_Parity_No;
    USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
    USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
    USART_Init ( USART1, &USART_InitStructure );
    USART_Cmd ( USART1, ENABLE );
}

#if PRINTF_ON
int fputc ( int ch, FILE *f ) {
    USART_SendData ( USART1, ( u8 ) ch );

    while ( USART_GetFlagStatus ( USART1, USART_FLAG_TC ) == RESET );

    return ch;
}
#endif
```

&emsp;&emsp;解决编译过程中的错误，归纳如下：

- `Uip/uip-split.c`：注释所有的`tcpip_output`函数，消除`uip_fw_output`函数的注释。
- `Uip/memb.c`：`memb_free`函数的返回值`return -1`改为`return 1`。
- `Apps/resolv.c`：`resolv_conf`函数中，把`resolv_conn = uip_udp_new(dnsserver, HTONS(53));`改为`resolv_conn = uip_udp_new((uip_ipaddr_t*)dnsserver, HTONS(53));`。

解决完所有问题后，编译成功后下载到`stm32`，进行`ping`测试。