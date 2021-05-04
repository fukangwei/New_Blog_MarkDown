---
title: Contiki在CC2530的移植
categories: Contiki和uip
date: 2019-02-04 23:38:25
---
&emsp;&emsp;首先选择一个`CC2530`的基础程序(例如`串口打印`)，移植`Contiki`系统的工作将在此基础上进行。<!--more-->
&emsp;&emsp;将`contiki-3.0/core/sys`目录下的`autostart.c`、`etimer.c`、`process.c`和`timer.c`文件，`contiki-3.0\cpu\cc253x\dev`目录下的`clock.c`，以及`contiki-3.0\cpu\cc253x\dev`目录下的`soc.c`添加到工程中。
&emsp;&emsp;将如下头文件路径添加到`IAR`中：

``` cpp
$PROJ_DIR$\contiki-3.0\core\
$PROJ_DIR$\contiki-3.0\core\lib\
$PROJ_DIR$\contiki-3.0\cpu\
$PROJ_DIR$\contiki-3.0\core\sys\
$PROJ_DIR$\contiki-3.0\platform\cc2530dk\
$PROJ_DIR$\contiki-3.0\cpu\cc253x\
```

&emsp;&emsp;将该串口打印程序的`main.c`文件修改为：

``` cpp
#include <sys/process.h>
#include <sys/procinit.h>
#include <sys/etimer.h>
#include <sys/autostart.h>
#include <sys/clock.h>
#include <ioCC2530.h>
#include "stdio.h"

#define LED1 P1_0 /* 定义P1.0口为LED1控制端 */

void IO_Init ( void ) {
    P1SEL &= ~0x01; /* P1.0作为普通IO口 */
    P1DIR |= 0x01; /* P1.0定义为输出 */
    P1INP |= 0X01; /* 打开三态 */
}

void InitUART ( void ) {
    PERCFG = 0x00; /* (外设控制寄存器)USART_0的I/O位置为备用位置0 */
    P0SEL = 0x0c; /* P0_2和P0_3用作串口(外部设备功能) */
    P2DIR &= ~0XC0; /* (端口2方向和端口0外设优先级控制)P0优先作为UART0 */
    U0CSR |= 0x80; /* 设置为UART模式 */
    U0GCR |= 8; /* 波特率指数值，BAUD_E和BAUD_M决定了UART波特率 */
    U0BAUD |= 59; /* 波特率设为9600 */
    UTX0IF = 0; /* UART0的TX中断标志初始设置为0 */
}

__near_func int putchar ( int c ) {
    UTX0IF = 0;
    U0DBUF = ( char ) c;

    while ( UTX0IF == 0 );

    return ( c );
}

PROCESS ( led_process, "led" );
PROCESS_THREAD ( led_process, ev, data ) {
    PROCESS_BEGIN();
    IO_Init();
    LED1 = 0;

    while ( 1 ) {
        static struct etimer et;
        etimer_set ( &et, CLOCK_SECOND / 2 );
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et ) );
        LED1 = !LED1; /* LED1闪烁 */
    }

    PROCESS_END();
}

PROCESS ( print_process, "print" );
PROCESS_THREAD ( print_process, ev, data ) {
    PROCESS_BEGIN();

    while ( 1 ) {
        static struct etimer et;
        etimer_set ( &et, CLOCK_SECOND / 2 );
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et ) );
        printf ( "I am running!\r\n" );
    }

    PROCESS_END();
}

unsigned int idle_count = 0;
AUTOSTART_PROCESSES ( &led_process, &print_process );

extern void soc_init();

int main ( void ) {
    InitUART();
    clock_init();
    soc_init();
    process_init();
    process_start ( &etimer_process, NULL );
    autostart_start ( autostart_processes );

    while ( 1 ) {
        do {
        } while ( process_run() > 0 );

        idle_count++;
        /* Idle! */
        /* Stop processor clock */
        /* asm("wfi"::); */
    }

    return 0;
}
```

对工程进行编译，可能会出现很多错误，接下来要进行修改。具体步骤如下：
&emsp;&emsp;1. 如果`IAR`提示`8051def.h`中的`stdint.h`不存在，则在`IAR`头文件路径中添加如下内容：

``` makefile
$TOOLKIT_DIR$\inc\dlib\c\
```

&emsp;&emsp;2. 注释`cc253x.h`中的`#include <compiler.h>`，然后加入`#include <ioCC2530.h>`。
&emsp;&emsp;3. 将`cc253x.h`中与`ioCC2530.h`重复的部分注释掉。
&emsp;&emsp;4. 将`contiki-conf.h`文件中的`CLOCK_CONF_STACK_FRIENDLY`设置为`0`。
&emsp;&emsp;5. 将`clock.c`中的`__xdata __at(0x0000) static unsigned long timer_value = 0;`改为`static volatile unsigned long timer_value = 0;`。
&emsp;&emsp;6. 将如下内容进行修改：

``` cpp
#pragma save
#if CC_CONF_OPTIMIZE_STACK_SIZE
    #pragma exclude bits
#endif

void clock_isr ( void ) __interrupt ( ST_VECTOR ) {
    /* ... */
}

#pragma restore
```

修改结果如下：

``` cpp
//#pragma save
//#if CC_CONF_OPTIMIZE_STACK_SIZE
//    #pragma exclude bits
//#endif

#pragma vector=ST_VECTOR
__near_func __interrupt void clock_isr ( void ) {
    /* ... */
}

//#pragma restore
```

&emsp;&emsp;7. 将`8051def.h`中的如下内容注释掉：

``` cpp
#if !defined(__SDCC_mcs51) && !defined(SDCC_mcs51)
#define __data
#define __xdata
#define __code
#define __bit bool
#define __sfr volatile unsigned char
#define __sbit volatile bool
#define __critical
#define __at(x)
#define __using(x)
#define __interrupt(x)
#define __naked
#endif
```

&emsp;&emsp;8. 在`autostart.h`中加入如下内容：

``` cpp
#define AUTOSTART_ENABLE 1
#define CC_NO_VA_ARGS    0
```

再次编译工程，如果没有报错，则移植成功。