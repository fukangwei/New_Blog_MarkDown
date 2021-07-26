---
title: STM32之NVIC
categories: STM32
date: 2018-12-30 07:38:05
---
### NVIC基础

&emsp;&emsp;`NVIC`是嵌套向量中断控制器，不可屏蔽中断(`NMI`)和外部中断都由它来处理。<!--more-->
&emsp;&emsp;`STM32`的中断有`2`个优先级，即`抢占式优先级`和`响应优先级`：

1. 高抢占式优先级的中断可以在低抢占式优先级的中断处理过程中被响应，即`中断嵌套`。
2. 当两个中断的抢占式优先级相同时，如果正在处理另一个中断，这个后到来的中断就要等到前一个中断处理完之后才能被处理。
3. 如果这两个中断同时到达，则中断控制器根据它们的响应优先级的高低来决定先处理哪一个。
4. 如果它们的抢占式优先级和响应优先级都相等，则根据它们在中断向量表中的排位顺序决定先处理哪一个。

### 优先级分组

&emsp;&emsp;`Cortex-M3`定义了`8`个比特位用于设置中断源的优先级，有如下分配方式：

- 所有`8`位用于指定响应优先级。
- 最高`1`位用于指定抢占式优先级，最低`7`位用于指定响应优先级。
- 最高`2`位用于指定抢占式优先级，最低`6`位用于指定响应优先级。
- 最高`3`位用于指定抢占式优先级，最低`5`位用于指定响应优先级。
- 最高`4`位用于指定抢占式优先级，最低`4`位用于指定响应优先级。
- 最高`5`位用于指定抢占式优先级，最低`3`位用于指定响应优先级。
- 最高`6`位用于指定抢占式优先级，最低`2`位用于指定响应优先级。
- 最高`7`位用于指定抢占式优先级，最低`1`位用于指定响应优先级。

&emsp;&emsp;`STM32`把指定中断优先级的寄存器位减少到`4`位，这`4`个寄存器位的分组方式如下：

- 第`0`组：所有`4`位用于指定响应优先级。
- 第`1`组：最高`1`位用于指定抢占式优先级，最低`3`位用于指定响应优先级。
- 第`2`组：最高`2`位用于指定抢占式优先级，最低`2`位用于指定响应优先级。
- 第`3`组：最高`3`位用于指定抢占式优先级，最低`1`位用于指定响应优先级。
- 第`4`组：所有`4`位用于指定抢占式优先级。

### NVIC_PriorityGroupConfig

&emsp;&emsp;`NVIC_PriorityGroupConfig`用于设置优先级分组：

``` cpp
void NVIC_PriorityGroupConfig ( uint32_t NVIC_PriorityGroup );
```

参数`NVIC_PriorityGroup`共有`5`种选项：

- `NVIC_PriorityGroup_0`：选择第`0`组。
- `NVIC_PriorityGroup_1`：选择第`1`组。
- `NVIC_PriorityGroup_2`：选择第`2`组。
- `NVIC_PriorityGroup_3`：选择第`3`组。
- `NVIC_PriorityGroup_4`：选择第`4`组。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
void NVIC_Config ( void ) {
    NVIC_InitTypeDef NVIC_InitStructure;
    NVIC_PriorityGroupConfig ( NVIC_PriorityGroup_1 );
    NVIC_InitStructure.NVIC_IRQChannel = TIM3_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1; /* 抢占式优先级为1 */
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1; /* 响应式优先级为1 */
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
    NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0; /* 抢占式优先级为0 */
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1; /* 响应式优先级为0 */
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
}
```