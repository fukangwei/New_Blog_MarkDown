---
title: STM32之NVIC
categories: 单片机
abbrlink: 92cf1c62
date: 2018-12-30 07:38:05
---
&emsp;&emsp;`NVIC`即嵌套向量中断控制器(`Nested Vectored Interrupt Controller`)。`STM32`的中有一个强大而方便的`NVIC`，它是属于`Cortex`内核的器件，不可屏蔽中断(`NMI`)和外部中断都由它来处理，而`SYSTICK`不是由`NVIC`来控制的。具有如下特性：

- `60`个可屏蔽中断通道(不包含`16`个`Cortex-M3`的中断线)。
- `16`个可编程的优先等级(使用了`4`位中断优先级)。
- 低延迟的异常和中断处理。
- 电源管理控制。
- 系统控制寄存器的实现。

&emsp;&emsp;`STM32`(`Cortex-M3`)中有两个优先级的概念：`抢占式优先级`和`响应优先级`。有人把响应优先级称作`亚优先级`或`副优先级`，每个中断源都需要被指定这两种优先级。
&emsp;&emsp;具有高抢占式优先级的中断可以在具有低抢占式优先级的中断处理过程中被响应，即`中断嵌套`，或者说高抢占式优先级的中断可以嵌套在低抢占式优先级的中断中。当两个中断源的抢占式优先级相同时，这两个中断将没有嵌套关系。当一个中断到来后，如果正在处理另一个中断，这个后到来的中断就要等到前一个中断处理完之后才能被处理。如果这两个中断同时到达，则中断控制器根据它们的响应优先级的高低来决定先处理哪一个。如果它们的抢占式优先级和响应优先级都相等，则根据它们在中断表中的排位顺序决定先处理哪一个。
&emsp;&emsp;`Cortex`内核具有强大的异常响应系统，它把能够打断当前代码执行流程的事件分为异常(`exception`)和中断(`interrupt`)，并把它们用一个表管理起来，编号为`0`至`15`的称为内核异常，而`16`以上的则称为外部中断，这个表就称为中断向量表。正是因为每个中断源都需要被指定这两种优先级，就需要有相应的寄存器位记录每个中断的优先级。`Cortex-M3`定义了`8`个比特位用于设置中断源的优先级，这`8`个比特位可以有`8`种分配方式：

- 所有`8`位用于指定响应优先级。
- 最高`1`位用于指定抢占式优先级，最低`7`位用于指定响应优先级。
- 最高`2`位用于指定抢占式优先级，最低`6`位用于指定响应优先级。
- 最高`3`位用于指定抢占式优先级，最低`5`位用于指定响应优先级。
- 最高`4`位用于指定抢占式优先级，最低`4`位用于指定响应优先级。
- 最高`5`位用于指定抢占式优先级，最低`3`位用于指定响应优先级。
- 最高`6`位用于指定抢占式优先级，最低`2`位用于指定响应优先级。
- 最高`7`位用于指定抢占式优先级，最低`1`位用于指定响应优先级。

以上便是优先级分组的概念，但是`Cortex-M3`允许具有较少中断源时使用较少的寄存器位指定中断源的优先级。而`STM32`对这个表重新进行了编排，把编号从`-3`至`6`的中断向量定义为`系统异常`，编号为负的内核异常不能被设置优先级，如`复位`(`Reset`)、`不可屏蔽中断`(`NMI`)、`硬错误`(`Hardfault`)。从编号`7`开始的为`外部中断`，这些中断的优先级都是可以被用户更改的。详细的`STM32`中断向量号可以在 `startup_stm32f10x_XX.s`中查找。
&emsp;&emsp;`STM32`把指定中断优先级的寄存器位减少到`4`位，这`4`个寄存器位的分组方式如下：

- 第`0`组：所有`4`位用于指定响应优先级(`16`种)。
- 第`1`组：最高`1`位用于指定抢占式优先级，最低`3`位用于指定响应优先级(`8`种)。
- 第`2`组：最高`2`位用于指定抢占式优先级，最低`2`位用于指定响应优先级(`4`种)。
- 第`3`组：最高`3`位用于指定抢占式优先级，最低`1`位用于指定响应优先级(`2`种)。
- 第`4`组：所有`4`位用于指定抢占式优先级。

这里便对应于固件库里相关的函数了，即`NVIC_PriorityGroupConfig(u32 NVIC_PriorityGroup)`。函数参数`NVIC_PriorityGroup`共有`5`种：

- `NVIC_PriorityGroup_0`：选择第`0`组。
- `NVIC_PriorityGroup_1`：选择第`1`组。
- `NVIC_PriorityGroup_2`：选择第`2`组。
- `NVIC_PriorityGroup_3`：选择第`3`组。
- `NVIC_PriorityGroup_4`：选择第`4`组。

&emsp;&emsp;这其实也很好理解，比如选择`NVIC_PriorityGroup_1`，那么抢占式优先级便占一位，也就是说可以有`2^1`个级别，可以设置为`0`和`1`；而响应优先级则占`3`位，也就是说可以有`2^3`个选择，可以设置为`0`至`7`；总共来说就可以区别`16`(即`2 * 8`)种优先级了。
&emsp;&emsp;假如现在同时有两个抢占式优先级别相同的中断发生，那么处理的顺序是谁的响应优先级高则谁优先进入中断。另外这点是需要注意的，如果此时进入这个中断之后又来了一个抢占式优先级相同但是响应优先级更高的中断，这时也是不会打断已有的中断的。

``` c
void NVIC_Config ( void ) {
    NVIC_InitTypeDef NVIC_InitStructure;
#ifdef VECT_TAB_RAM
    // Set the Vector Table base location at 0x20000000
    NVIC_SetVectorTable ( NVIC_VectTab_RAM, 0x0 );
#else
    // Set the Vector Table base location at 0x08000000
    NVIC_SetVectorTable ( NVIC_VectTab_FLASH, 0x0 );
#endif
    /* 中断优先级组：1组(整个系统为同一组) */
    NVIC_PriorityGroupConfig ( NVIC_PriorityGroup_1 );
    /* 设置抢占式优先级0至1，响应式优先级0至7 */
    NVIC_InitStructure.NVIC_IRQChannel = TIM2_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
    // Enable the TIM3 Interrupt
    NVIC_InitStructure.NVIC_IRQChannel = TIM3_IRQn; /* TIM3全局中断 */
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1; /* 抢占式优先级为1 */
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1; /* 响应式优先级为1 */
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; /* IRQ通道被使能 */
    NVIC_Init ( &NVIC_InitStructure );
    NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0; /* 抢占式优先级为0 */
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1; /* 响应式优先级为0 */
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
}
```