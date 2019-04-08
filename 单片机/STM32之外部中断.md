---
title: STM32之外部中断
date: 2018-12-30 07:24:02
tags:
---
&emsp;&emsp;`STM32`的每个`IO`都可以作为外部中断的中断输入口，其中断控制器支持`19`个外部中断/事件请求。每个中断设有状态位，每个中断/事件都有独立的触发和屏蔽设置。`STM32F103`的`19`个外部中断为：

- 线`0`至`15`：对应外部`IO`口的输入中断。
- 线`16`：连接到`PVD`输出。
- 线`17`：连接到`RTC`闹钟事件。
- 线`18`：连接到`USB`唤醒事件。

&emsp;&emsp;在库函数中，配置`GPIO`与中断线映射关系的函数为`GPIO_EXTILineConfig`：

``` c
void GPIO_EXTILineConfig ( uint8_t GPIO_PortSource, uint8_t GPIO_PinSource );
```

该函数将`GPIO`端口与中断线映射起来：

``` c
GPIO_EXTILineConfig ( GPIO_PortSourceGPIOE, GPIO_PinSource2 );
```

将中断线`2`与`GPIOE`映射起来，那么很显然是`GPIOE.2`与`EXTI2`中断线连接了。设置好中断线映射之后，那么到底来自这个`IO`口的中断是通过什么方式触发的呢？接下来就要设置该中断线上中断的初始化参数了。
&emsp;&emsp;中断线上中断的初始化是通过函数`EXTI_Init`实现的，其函数原型为：

``` c
void EXTI_Init ( EXTI_InitTypeDef *EXTI_InitStruct );
```

使用示例如下：

``` c
EXTI_InitTypeDef EXTI_InitStructure;
/* 中断线的标号，取值范围为EXTI_Line0至EXTI_Line15 */
EXTI_InitStructure.EXTI_Line = EXTI_Line4;
/* 中断模式，可选值为中断EXTI_Mode_Interrupt和事件EXTI_Mode_Event */
EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;
/* 触发方式，可以是下降沿触发EXTI_Trigger_Falling、上升沿触发EXTI_Trigger_Rising
   或者任意电平(上升沿和下降沿)触发EXTI_Trigger_Rising_Falling */
EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Falling;
EXTI_InitStructure.EXTI_LineCmd = ENABLE;
EXTI_Init ( &EXTI_InitStructure );
```

既然是外部中断，涉及到中断问题，当然还要设置`NVIC`中断优先级：

``` c
NVIC_InitTypeDef NVIC_InitStructure;
NVIC_InitStructure.NVIC_IRQChannel = EXTI2_IRQn;
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0x02;
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0x02;
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
NVIC_Init ( &NVIC_InitStructure );
```

配置完中断优先级之后，接着要做的就是编写中断服务函数。中断服务函数的名字在`MDK`中是定义好的。`STM32`的`IO`口外部中断函数只有`6`个：

``` c
EXPORT EXTI0_IRQHandler
EXPORT EXTI1_IRQHandler
EXPORT EXTI2_IRQHandler
EXPORT EXTI3_IRQHandler
EXPORT EXTI4_IRQHandler
EXPORT EXTI9_5_IRQHandler
EXPORT EXTI15_10_IRQHandler
```

对于中断线`0`至`4`，每个中断线对应一个中断函数；中断线`5`至`9`共用中断函数`EXTI9_5_IRQHandler`，中断线`10`至`15`共用中断函数`EXTI15_10_IRQHandler`。
&emsp;&emsp;在编写中断服务函数的候，会经常使用到两个函数。第一个函数是判断某个中断线上的中断是否发生(标志位是否置位)：

``` c
ITStatus EXTI_GetITStatus ( uint32_t EXTI_Line );
```

这个函数一般使用在中断服务函数的开头判断中断是否发生。另一个函数是清除某个中断线上的中断标志位：

``` c
void EXTI_ClearITPendingBit ( uint32_t EXTI_Line );
```

这个函数一般应用在中断服务函数结束之前，清除中断标志位。常用的中断服务函数格式为：

``` c
void EXTI2_IRQHandler ( void ) {
    if ( EXTI_GetITStatus ( EXTI_Line2 ) != RESET ) { /* 判断某个线上的中断是否发生 */
        /* 中断逻辑 */
        EXTI_ClearITPendingBit ( EXTI_Line2 ); /* 清除LINE上的中断标志位 */
    }
}
```

&emsp;&emsp;使用`IO`口外部中断的一般步骤：

1. 初始化`IO`口为输入。
2. 开启`IO`口复用时钟，设置`IO`口与中断线的映射关系。
3. 初始化线上中断，设置触发条件等。
4. 配置中断分组(`NVIC`)，并使能中断。
5. 编写中断服务函数。

&emsp;&emsp;**补充说明**：`STM32`外部中断实现`在需要的时候打开，不需要的时候屏蔽`的功能如下所示：

``` c
EXTI->IMR &= ~( EXTI_Line4 ); /* 屏蔽外部中断4 */
EXTI->IMR != EXTI_Line4; /* 开启外部中断4 */
```