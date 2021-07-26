---
title: STM32之基本定时器
date: 2021-07-30 06:35:36
categories: STM32
---
### 定时器分类

&emsp;&emsp;`STM32`的定时器有如下分类：<!--more-->

- `基本定时器`：`TIM6`、`TIM7`。
- `通用定时器`：`TIM2`、`TIM3`、`TIM4`、`TIM5`。
- `高级控制定时器`：`TIM1`、`TIM8`。

### TIM_TimeBaseInitTypeDef

&emsp;&emsp;`TIM_TimeBaseInitTypeDef`的定义如下：

``` cpp
typedef struct {
    u16 TIM_Period;
    u16 TIM_Prescaler;
    u8 TIM_ClockDivision;
    u16 TIM_CounterMode;
    u8 TIM_RepetitionCounter;
} TIM_TimeBaseInitTypeDef;
```

- `TIM_Period`：定时器周期，当计数寄存器的值递增到等于该值时，将相关事件标志位置位。
- `TIM_Prescaler`：定时器预分频设置，时钟源经该预分频器才是定时器时钟。
- `TIM_ClockDivision`：时钟分频，基本定时器没有这个功能。
- `TIM_CounterMode`：定时器计数方式设置，基本定时器只能向上计数。
- `TIM_RepetitionCounter`：重复计数器，基本定时器没有这个功能。

### 定时时间

&emsp;&emsp;定时器的实际时钟计算如下：

``` cpp
定时器的实际时钟 = 内部时钟 / (定时器预分频 + 1)
```

&emsp;&emsp;基本定时器只有内部时钟`72MHz`，当`TIM_Prescaler`设置为`7200 - 1`时，定时器的实际时钟为`72MHz / ((7200 - 1) + 1) = 10kHz`。
&emsp;&emsp;当`TIM_Period`设置为`5000 - 1`时，则定时时间为`5000 * (1 / 10kHz) = 0.5s`。