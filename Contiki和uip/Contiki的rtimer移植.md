---
title: Contiki的rtimer移植
date: 2019-02-04 23:00:12
tags:
---
### 移植基础

&emsp;&emsp;`rtimer`即`real-time timer`，在特定时间调用特定函数，用于处理一些对时间敏感的事件。相比较`etimer`而言，`rtimer`是细粒度的(`10KHz`)定时器。但是对于细粒度定时器，若频繁产生中断，则会比较消耗`CPU`资源，因此在实现`rtimer`时应尽可能减少中断调用。
&emsp;&emsp;对于`Contiki`系统，它已经在`/core/sys/rtimer.h`和`rtimer.c`中对`rtimer`相关结构进行了定义。对于特定平台而言，主要需要实现以下几个函数：

- `rtimer_arch_init( void )`：针对特定平台的初始化操作，被`rtimer_init`函数调用。
- `rtimer_arch_now( void )`：用于获取当前的`rtimer`时间。
- `rtimer_arch_schedule ( rtimer_clock_t t )`：传递一个唤醒时间，在特定时刻进行调度操作，调用`rtimer_run_next`。

&emsp;&emsp;同时需要在`rtimer_arch.h`中定义`RTIMER_ARCH_SECOND`确定`rtimer`每秒的滴答数。

### STM32移植准备

&emsp;&emsp;移植过程中，首先需要确定`rtimer`每秒的滴答数是多少。在本系统中，达到`100us`的精度就可以了，因此选用了`10KHz`的频率。并且在`10KHz`的情况下，`16bit`计数而言可以达到`6.5s`的计时，能够满足一般的应用。
&emsp;&emsp;选定用于实现`rtimer`的定时器。`STM32`具备多个定时器，且多个定时器基本都能够满足这样的需求。因此，在本系统中选用了`TIM3`，并利用`TIM3`的`TIM_IT_CC1`中断来完成`rtiemr_run_next`的调度。

### rtimer移植

&emsp;&emsp;`timer`的初始化：

``` cpp
void rtimer_arch_init ( void ) {
    uint16_t prescaler = ( uint16_t ) ( SystemCoreClock  / RTIMER_ARCH_SECOND ) - 1;
    RCC_APB1PeriphClockCmd ( RCC_APB1Periph_TIM3, ENABLE );
    TIM_TimeBaseStructure.TIM_Period = 65535;
    TIM_TimeBaseStructure.TIM_Prescaler = 0;
    TIM_TimeBaseStructure.TIM_ClockDivision = 0;
    TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up;  
    TIM_TimeBaseInit ( TIM3, &TIM_TimeBaseStructure );
    TIM_PrescalerConfig ( TIM3, prescaler, TIM_PSCReloadMode_Immediate );
    TIM_Cmd ( TIM3, ENABLE );
    rtimer_arch_disable_irq();
    NVIC_InitStructure.NVIC_IRQChannel = TIM3_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 3;  
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
}
```

&emsp;&emsp;实现`rtimer_arch_schedule`函数：在此函数中，需要确定定时器的计数时间，同时使能定时器，并配置`CCR1`的值，开启定时器中断，等待中断调度。`rtimer_arch_schedule`函数实际在`rtimer_set`中被调用，当设置`rtimer`相关回调时配置，但是一次仅仅能够配置一个`rtimer`：

``` cpp
void rtimer_arch_schedule ( rtimer_clock_t t ) {
    rtimer_arch_enable_irq();
    PRINTF ( "rtimer_arch_schedule time %u\r\n", t );
    TIM_SetCompare1 ( TIM3, t );
}
```

中断使能直接封装`rtimer_arch_enable_irq`与`rtimer_arch_disable_irq`函数：

``` cpp
void rtimer_arch_disable_irq ( void ) {
    TIM_ITConfig ( TIM3, TIM_IT_CC1, DISABLE );
}
​
void rtimer_arch_enable_irq ( void ) {
    TIM_ITConfig ( TIM3, TIM_IT_CC1, ENABLE );
}
```

&emsp;&emsp;中断调度处理如下：

``` cpp
void TIM3_IRQHandler ( void ) { /* TIM3中断 */
    /* 检查指定的TIM中断发生与否 */
    if ( TIM_GetITStatus ( TIM3, TIM_IT_CC1 ) != RESET ) {
        /* 清除TIMx的中断待处理位 */
        TIM_ClearITPendingBit ( TIM3, TIM_IT_CC1 );
        rtimer_arch_disable_irq();
        rtimer_run_next();
    }
}
```

### rtimer使用示例

&emsp;&emsp;首先开启一个线程，配置一个`rtimer`定时器`ex_timer`，定时器时间到之后调用`led_on`函数。同时在`led_on`中重新配置`ex_timer`回调为`led_off`，当`led_off`调用之后又配置为`led_on`。如此循环配置，从而实现一个简单的`LED`闪烁操作：

``` cpp
struct rtimer ex_timer;
static void led_on ( struct rtimer *t, void *ptr );
static void led_off ( struct rtimer *t, void *ptr );
​
static void led_on ( struct rtimer *t, void *ptr ) {
    LED = 0;
    rtimer_set ( &ex_timer, 1000, 0, led_off, NULL );
}
​
static void led_off ( struct rtimer *t, void *ptr ) {
    LED = 1;
    rtimer_set ( &ex_timer, 1000, 0, led_on, NULL );
}
​
PROCESS ( rtimer_ex_process, "rtimer_ex_process" );
PROCESS_THREAD ( rtimer_ex_process, ev, data ) {
    PROCESS_BEGIN();
    rtimer_set ( &ex_timer, 1000, 0, led_on, NULL );
    PROCESS_END();
}
```