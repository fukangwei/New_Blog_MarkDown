---
title: Contiki系统定时器
categories: Contiki和uip
date: 2019-02-05 08:40:07
---
### Timers

&emsp;&emsp;`Contiki`系统提供了一套时钟库用于应用程序和系统本身。时钟库包含了检查时间超出、将系统从低功耗模式唤醒到预定时间，以及实时任务安排等功能。时钟也用于应用程序，让系统和其他一起工作，或者在恢复执行前进入低功耗模式一段时间。<!--more-->

### The Contiki Timer Modules

&emsp;&emsp;`Contiki`有一个时钟模块和一套时钟：`timer`、`stimer`、`ctimer`、`etimer`和`rtimer`。不同的时钟有不同的用处，有的时钟提供了长运行时间低密度(时间间隔长)，有的时钟提供了短运行时间和高密度(时间间隔短)，有的时钟可以用在中断上下文(`rtimer`)，而其他时钟则不行。时钟模块提供了操作系统时间的功能，以及短时间阻塞`CPU`的功能。定时器库是实现时钟模块的功能的基础。
&emsp;&emsp;`timer`和`stimer`库提供了最简单形式的定时器，用于检查一段时间是否到期。应用程序需要询问计时器，它们是否已经过期。然而两者的区别在于：`timer`使用系统嘀嗒，而`stimer`使用秒，允许更长的时间。不同于其他定时器，`timer`和`stimer`库可以从中断中安全地使用，这使得它们在底层驱动中特别有用。
&emsp;&emsp;`Etimer`库提供事件时间，用于执行`Contiki`进程在一段时间后的计划事件。它用于`Contiki`的进程中，等待一段时间，此时其他的部分可以工作或进入低功耗模式。
&emsp;&emsp;`Ctimer`提供回调时间，用于在一段时间之后安排调用回调函数。就像事件定时器一样，它们用来等待一些时间，而在这段时间内，系统其他的部分可以工作或进入低功耗模式。当时间到期之后，回调定时器调用函数，`they are especially useful in any code that do not have an explicit Contiki process such as protocol implementations`。在其他方面，使用的回调定时器在`Rime`协议栈处理通信超时。
&emsp;&emsp;`Rtimer`库提供实时任务调度。`Rtimer`库抢占任何正在运行的`Contiki`进程，让实时任务在预定的时间里执行。

### The Clock Module

&emsp;&emsp;时钟模块提供操作系统时间的功能。
&emsp;&emsp;`Contiki`时钟模块的`API`接口如下：`clock_time`函数以时钟嘀嗒的形式返回当前系统时间。每秒时钟嘀嗒的数是和平台相关的，通常被指定为常数`CLOCK_SECOND`。系统时间被指定为和平台相关的类型`clock_time_t`，在大多数情况下这是一个有限的无符号值，运行时会变很大。时钟模块也提供`clock_seconds`函数，以秒的形式获得系统时间，其值为一个无符号的长整型数，这个时间值会变的很大，直到它增加到最大(在`MSP430`平台上为`136`年)，然后系统重新开始，时间也从零开始。
&emsp;&emsp;时钟模块提供两个函数阻塞`CPU`：`clock_delay`阻塞`CPU`一个指定的延迟，`clock_wait`阻塞`CPU`一个指定的时钟嘀嗒。这些函数通常只用于底层驱动程序，尤其是在有必要等待很短的时间，但并不放弃控制`CPU`的情况。
&emsp;&emsp;函数`clock_init`由系统启动，初始化时钟模块的时候调用。

### The Timer Library

&emsp;&emsp;`Contiki`时钟库提供设置、重置、重启时钟的函数，并检查一个时钟是否到期。一个应用程序需要`手动`地检查定时器是否到期，而不是自动完成的。在时钟模块中，时钟库使用`clock_timer`获得当前的系统时间。定时器被声明为`struct`类型，所有访问定时器都是经过指针指向被声明的定时器。`Contiki`定时器库的`API`如下：

- `void timer_set ( struct timer *t, clock_time_t interval )`：启动定时器。
- `void timer_reset ( struct timer *t )`：从以前到期时间重新启动定时器。
- `void timer_restart ( struct timer *t )`：从当前时间重启定时器。
- `int timer_expired ( struct timer *t )`：检查定时器是否到期。
- `clock_time_t timer_remaining ( struct timer *t )`：获得剩余时间。

&emsp;&emsp;定时器由`timer_set`完成初始化，设置定时器从当前时间到指定时间的延迟，而且它还存储了定时器的时间间隔。`Timer_reset`可以从之前的到期时间重置定时器，`timer_restart`从当前时间重新启动定时器。`Timer_reset`和`timer_restart`都是调用`timer_set`，用时间间隔设置定时器。这些函数的区别是：`timer_reset`用完全相同的时间间隔设置定时器延时，而`timer_restart`从当前时间设置时间间隔。`Timer_expired`函数用来检查定时器是否到期，`timer_remaining`用于获得一个定时器到期的剩余时间。如果定时器已经过期，它的返回值未知的。
&emsp;&emsp;`Timer`库可以在中断中安全地使用。下面的代码显示了一个定时器如何在中断中检测超时。

``` cpp
static struct timer rxtimer;

void init ( void ) {
    timer_set ( &rxtimer, CLOCK_SECOND / 2 );
}

interrupt ( UART1RX_VECTOR )
uart1_rx_interrupt ( void ) {
    if ( timer_expired ( &rxtimer ) ) {
        /* Timeout */
    }

    timer_restart ( &rxtimer );
}
```

### The Stimer Library

&emsp;&emsp;`Contiki`的`Stimer`库提供的定时机制类似于`timer`库，但是它的时间使用是秒，允许更长的到期时间。`stimer`库在时钟模块中用`clock_seconds`以秒的形式获得当前的系统时间。
&emsp;&emsp;`Stimer`库的`API`如下，它非常类似于`timer`的库。不同的是，它以秒为单位，而`timer`是以系统嘀嗒为单位。`Stimer`库可以在中断中安全地使用。

- `void stimer_set ( struct stimer *t, unsigned long interval )`：启动`timer`。
- `void stimer_reset ( struct stimer *t )`：从到期时间中重启`timer`。
- `void stimer_restart ( struct stimer *t )`：从当前时间重启`timer`。
- `int stimer_expired ( struct stimer *t )`：检查时间是否到期。
- `unsigned long stimer_remaining ( struct stimer *t )`：获得剩余时间。

### The Etimer Library

&emsp;&emsp;`Contiki`的`etimer`库提供了一个定时器机制，产生定时事件。当事件时间到期时，事件定时器将向进程标示`PROCESS_EVENT_TIMER`来设置定时器。在时钟模块中，`Etimer`库使用`clock_time`获得系统当前时间。事件定时器声明为`struct etimer`类型，所有访问事件定时器都需要通过指针来指向被声明的`etimer`时间。`Etimer`库的`API`如下：

- `void etimer_set ( struct etimer *t, clock_time_t interval )`：启动定时器。
- `void etimer_reset ( struct etimer *t )`：从以前到期时间重启定时器。
- `void etimer_restart ( struct etimer *t )`：从当前时间重启定时器。
- `void etimer_stop ( struct etimer *t )`：停止定时器。
- `int etimer_expired ( struct etimer *t )`：检查时间是否到期。
- `int etimer_pending( void )`：检查是否有非过期的事件计时器。
- `clock_time_t etimer_next_expiration_time( void )`：得到下一个事件定时器过期时间。
- `void etimer_request_poll( void )`：通知`etimer`库，系统时间已经改变。

&emsp;&emsp;事件定时器调用`etimer_set`进行初始化，设置定时器从当前时间开始到指定时间的延时。`etimer_reset`可以从之前的到期时间启动定时器；`Etimer_restart`从当前时间重启定时器，它们都使用相同的时间间隔，且最初都是由`etimer_set`设置。`etimer_reset`和`etimer_restart`的区别在于：前者的时间是从以前的到期时间，而后者的时间是从当前时间开始。一个事件定时器可以被`etimer_stop`停止，这意味着`etimer`立即过期，而不会产生一个定时器事件；`Etimer_expired`用来检查一个`etimer`时间是否过期。`Etimer`库不能在中断中安全地使用。
&emsp;&emsp;下面演示如何用`etimer`安排`process`每秒运行一次：

``` cpp
PROCESS_THREAD ( example_process, ev, data ) {
    static struct etimer et;
    PROCESS_BEGIN();
    etimer_set ( &et, CLOCK_SECOND ); /* Delay 1 second */

    while ( 1 ) {
        PROCESS_WAIT_EVENT_UNTIL ( etimer_expired ( &et ) );
        etimer_reset ( &et ); /* Reset the etimer to trig again in 1 second */
        /* ... */
    }

    PROCESS_END();
}
```

### Porting the Etimer Library

&emsp;&emsp;`Etimer`库实现的核心是`/sys/etimer.c`，与平台无关，但需要回调`etimer_request_poll`来处理事件定时器。这允许事件定时器到期时，从低功耗模式唤醒。`Etimer`库提供三种功能：

- `etimer_pending`：检查是否有任何非过期事件定时器。
- `etimer_next_expiration_time`：得到下一个事件定时器过期时间。
- `etimer_request_poll`：通知`etimer`库，系统时间已经改变，一个`etimer`已经过期。这个函数从中断调用是安全的。

&emsp;&emsp;时钟模块处理系统时间之后，通常还要回调`etimer`库(原文是`The implementation of the clock module usually also handles the callbacks to the etimer library since the module already handles the system time`)。可以通过定期调用`etimer_request_poll`简单地实现，或者利用`etime_next_expiration_time`，或者在需要时通知`etimer`库。

### The Ctimer Library

&emsp;&emsp;`Contiki`的`ctimer`库提供了一个定时器机制，当回调时间过期时，调用指定的函数。在时钟模块中，`Ctimer`库使用`clock_timer`获得当前的系统时间。`Ctimer`库的实现使用了`etimer`库。
&emsp;&emsp;`Ctimer`库的`API`如下，它和`etimer`的库很像，区别在于`ctimer_set`需要一个回调函数指针和数据指针作为参数。当`ctimer`到期时，它将数据指针作为参数调用回调函数。`Ctimer`库在中断中使用不是安全的。

- `void ctimer_set ( struct ctimer *c, clock_time_t t, void ( *f ) ( void * ), void *ptr )`：启动定时器。
- `void ctimer_reset ( struct ctimer *t )`：从以前到期的时间重启定时器。
- `void ctimer_restart ( struct ctimer *t )`：从当前时间重启定时器。
- `void ctimer_stop ( struct ctimer *t )`：停止定时器。
- `int ctimer_expired ( struct ctimer *t )`：检查定时器是否过期。

&emsp;&emsp;下面的代码展示了`ctimer`如何安排回调函数每秒调用一次：

``` cpp
static void callback ( void *ptr ) {
    ctimer_reset ( &timer );
    /* ... */
}

void init ( void ) {
    ctimer_set ( &timer, CLOCK_SECOND, callback, NULL );
}
```

### The Rtimer Library

&emsp;&emsp;`Contiki`的`rtimer`库提供了实时任务调度和执行(可预测执行时间)。`Rtimer`使用自己的时钟模块调度，允许更高的时钟分辨率。`RTIMER_SECOND`函数以嘀嗒的形式获取当前系统时间，`RTIMER_SECOND`指定每秒的时钟节拍数。不像其他的`Contiki`定时器库，实时任务抢占正常执行的进程，立即执行任务。

### Porting the Rtimer Library

&emsp;&emsp;`Rtimer`库实现的核心是`/sys/rtimer.c`，与平台无关，取决于`rtime-arch.c`处理平台的相关功能，如任务调度等。`rtimer`架构代码需要定义`RTIMER_ARCH_SECOND`作为每秒的滴答数，`rtimer_clock_t`数据类型用于`rtimer`时间，这些都是在`rtimer-arch.h`文件中声明的。`Rtimer`库与平台相关的函数如下：

- `RTIMER_ARCH_SECOND`：每秒的滴答数。
- `void rtimer_arch_init ( void )`：初始化`rtimer`。
- `rtimer_clock_t rtimer_arch_now( void )`：获取当前时间。
- `int rtimer_arch_schedule ( rtimer_clock_t wakeup_time )`：安排一个`rtimer_run_next`调用。