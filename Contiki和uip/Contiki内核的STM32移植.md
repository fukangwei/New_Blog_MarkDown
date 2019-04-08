---
title: Contiki内核的STM32移植
date: 2019-02-05 07:24:14
tags:
---
&emsp;&emsp;1. 找一个`STM32`的`UART`的打印例程，最好是支持`printf`函数的。
&emsp;&emsp;2. 接下来拷贝`contiki\core`中的文件。要加入工程的文件只有如下几个：`core\sys`目录下的`autostart.c`、`etimer.c`、`process.c`和`timer.c`。
&emsp;&emsp;3. 在`include`路径中，加入`contiki\core`、`contiki\core\sys`、`contiki\core\lib`、`contiki\cpu`。
&emsp;&emsp;4. 把`cpu\arm\stm32f103`目录下的`clock.c`拷贝到工程目录(`stm32f10x_it.c`所在目录)并加入工程中。
&emsp;&emsp;5. 把`platform\stm32test`目录下的`contiki_main.c`复制到`main.c`中，并将该目录下的`contiki_conf.h`加入到`include`路径中。
&emsp;&emsp;6. 把`uart`的文件改成`debug-uart.c`，并将相应的`.h`文件也改了，重新放入工程中。
&emsp;&emsp;7. 把`clock.c`中两个函数做如下更改：用`#include "stm32f10x.h"`和`#include "stm32f10x_it.h"`替换原来的`#include <stm32f10x_map.h>`和`#include <nvic.h>`。其他源文件和头文件也做此修改。把`systick`初始化函数改成：

``` cpp
void clock_init() {
    if ( SysTick_Config ( SystemCoreClock / CLOCK_SECOND ) ) {
        while ( 1 );
    }
}
```

把`systick`中断函数改为：

``` cpp
void SysTick_Handler ( void ) {
    current_clock++;
​
    if ( etimer_pending() && etimer_next_expiration_time() <= current_clock ) {
        etimer_request_poll();
        // printf( "%d, %d\n", clock_time(), etimer_next_expiration_time() );
    }
​
    if ( --second_countdown == 0 ) {
        current_seconds++;
        second_countdown = CLOCK_SECOND;
    }
}
```

最后把`stm32f10x_it.c`的`void SysTick_Handler(void){}`删除。
&emsp;&emsp;8. 把串口初始化函数改为：

``` cpp
void dbg_setup_uart ( void ) {
    USART_InitTypeDef USART_InitStructure;
    GPIO_InitTypeDef GPIO_InitStructure;
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA | RCC_APB2Periph_USART1 | RCC_APB2Periph_AFIO, ENABLE );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    USART_InitStructure.USART_BaudRate = 9600;
    USART_InitStructure.USART_WordLength = USART_WordLength_8b;
    USART_InitStructure.USART_StopBits = USART_StopBits_1;
    USART_InitStructure.USART_Parity = USART_Parity_No;
    USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
    USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
    USART_Init ( USART1, &USART_InitStructure );
    USART_ITConfig ( USART1, USART_IT_RXNE, ENABLE );
    USART_Cmd ( USART1, ENABLE );
}
```

&emsp;&emsp;9. 将`main.c`中的`#include<gpio.h>`删除，并编译工程。可能会出一些错误，比如多定义或者未定义。其中`1`个是`autostart_processes`未定义，解决方法是创建一个进程并使用`AUTOSTART_PROCESSES`加入自启动项目。`main.c`如下：

``` cpp
#include "stm32f10x.h"
#include "stm32f10x_it.h"
#include <stm32f10x_dma.h>
#include <stdint.h>
#include <stdio.h>
#include <debug-uart.h>
#include <sys/process.h>
#include <sys/procinit.h>
#include <sys/etimer.h>
#include <sys/autostart.h>
#include <sys/clock.h>
#include "led.h"
​
PROCESS ( blink_process, "Blink" );
PROCESS_THREAD ( blink_process, ev, data ) {
    PROCESS_BEGIN();
​
    while ( 1 ) {
        static struct etimer et;
        etimer_set ( &et, CLOCK_SECOND );
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et ) );
        GPIO_ResetBits ( GPIOA, GPIO_Pin_8 );
        etimer_set ( &et, CLOCK_SECOND );
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et ) );
        GPIO_SetBits ( GPIOA, GPIO_Pin_8 );
    }
​
    PROCESS_END();
}
​
PROCESS ( blink_process_2, "Blink" );
PROCESS_THREAD ( blink_process_2, ev, data ) {
    PROCESS_BEGIN();
​
    while ( 1 ) {
        static struct etimer et_2;
        etimer_set ( &et_2, CLOCK_SECOND / 2 );
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et_2 ) );
        GPIO_ResetBits ( GPIOD, GPIO_Pin_2 );
        etimer_set ( &et_2, CLOCK_SECOND / 2 );
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et_2 ) );
        GPIO_SetBits ( GPIOD, GPIO_Pin_2 );
    }
​
    PROCESS_END();
}
​
unsigned int idle_count = 0;
AUTOSTART_PROCESSES ( &blink_process, &blink_process_2 );
​
int main ( void ) {
    LED_Init();
    dbg_setup_uart();
    printf ( "Initialising\n" );
    clock_init();
    process_init();
    process_start ( &etimer_process, NULL );
    autostart_start ( autostart_processes );
    printf ( "Processes running\n" );
​
    while ( 1 ) {
        do {
        } while ( process_run() > 0 );
​
        idle_count++;
        /* Idle! */
        /* Stop processor clock */
        /* asm("wfi"::); */
    }
​
    return 0;
}
```