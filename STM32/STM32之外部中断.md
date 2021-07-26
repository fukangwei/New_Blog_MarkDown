---
title: STM32之外部中断
categories: STM32
date: 2018-12-30 07:24:02
---
&emsp;&emsp;`STM32`的每个`IO`都可以作为外部中断的中断输入口。`GPIO`的管脚`GPIOx.0`至`GPIOx.15`分别对应中断线`0`至`15`。<!--more-->

### GPIO_EXTILineConfig

&emsp;&emsp;该函数将`GPIO`端口与中断线进行映射：

``` cpp
void GPIO_EXTILineConfig ( uint8_t GPIO_PortSource, uint8_t GPIO_PinSource );
```

将`GPIOE.2`与`EXTI2`中断线进行连接：

``` cpp
GPIO_EXTILineConfig ( GPIO_PortSourceGPIOE, GPIO_PinSource2 );
```

### EXTI_Init

&emsp;&emsp;中断线的初始化是通过`EXTI_Init`实现的：

``` cpp
void EXTI_Init ( EXTI_InitTypeDef *EXTI_InitStruct );
```

使用示例：

``` cpp
EXTI_InitTypeDef EXTI_InitStructure;
EXTI_InitStructure.EXTI_Line = EXTI_Line4; /* 中断线的标号，取值范围为EXTI_Line0至EXTI_Line15 */
EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt; /* 中断模式，可选值为EXTI_Mode_Interrupt和EXTI_Mode_Event */
EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Falling; /* 触发方式 */
EXTI_InitStructure.EXTI_LineCmd = ENABLE;
EXTI_Init ( &EXTI_InitStructure );
```

&emsp;&emsp;`EXTI_Trigger`有如下选项：

- `EXTI_Trigger_Falling`：下降沿触发。
- `EXTI_Trigger_Rising`：上升沿触发。
- `EXTI_Trigger_Rising_Falling`：任意电平触发。

### 中断优先级

&emsp;&emsp;既然是外部中断，涉及到中断问题，当然还要设置`NVIC`中断优先级：

``` cpp
NVIC_InitTypeDef NVIC_InitStructure;
NVIC_InitStructure.NVIC_IRQChannel = EXTI2_IRQn;
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0x02;
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0x02;
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
NVIC_Init ( &NVIC_InitStructure );
```

### 中断服务函数

&emsp;&emsp;配置完中断优先级之后，接着就要编写中断服务函数。
&emsp;&emsp;中断服务函数的名字在`MDK`中是定义好的。`STM32`的`IO`口外部中断函数只有`6`个：

``` cpp
EXTI0_IRQHandler
EXTI1_IRQHandler
EXTI2_IRQHandler
EXTI3_IRQHandler
EXTI4_IRQHandler
EXTI9_5_IRQHandler
EXTI15_10_IRQHandler
```

- 对于中断线`0`至`4`，每个中断线分别对应一个中断函数。
- 对于中断线`5`至`9`，共用中断函数`EXTI9_5_IRQHandler`。
- 对于中断线`10`至`15`，共用中断函数`EXTI15_10_IRQHandler`。

&emsp;&emsp;常用的中断服务函数格式如下：

``` cpp
void EXTI2_IRQHandler ( void ) {
    if ( EXTI_GetITStatus ( EXTI_Line2 ) != RESET ) { /* 判断Line2上的中断是否发生 */
        /* 中断逻辑 */
        EXTI_ClearITPendingBit ( EXTI_Line2 ); /* 清除Line2上的中断标志位 */
    }
}
```

### EXTI_GetITStatus

&emsp;&emsp;`EXTI_GetITStatus`用于判断某个中断线上的中断是否发生：

``` cpp
ITStatus EXTI_GetITStatus ( uint32_t EXTI_Line );
```

### EXTI_ClearITPendingBit

&emsp;&emsp;`EXTI_ClearITPendingBit`用于清除某个中断线上的中断标志位：

``` cpp
void EXTI_ClearITPendingBit ( uint32_t EXTI_Line );
```

### 补充说明

&emsp;&emsp;`STM32`外部中断实现`在需要的时候打开，不需要的时候屏蔽`的功能：

``` cpp
EXTI->IMR &= ~( EXTI_Line4 ); /* 屏蔽外部中断4 */
EXTI->IMR != EXTI_Line4; /* 开启外部中断4 */
```