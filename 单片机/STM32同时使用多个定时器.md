---
title: STM32同时使用多个定时器
categories: 单片机
abbrlink: 44465f3f
date: 2018-12-29 17:54:42
---
&emsp;&emsp;代码如下：

``` c
void TIM2_IRQHandler ( void ) {
    if ( TIM_GetITStatus ( TIM2, TIM_IT_Update ) != RESET ) {
        /* user code */
    }
​
    TIM_ClearITPendingBit ( TIM2, TIM_IT_Update );
}
​
void TIM2_Int_Init ( u16 arr, u16 psc ) {
    TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;
    NVIC_InitTypeDef NVIC_InitStructure;
    TIM_TimeBaseStructure.TIM_Period = arr;
    TIM_TimeBaseStructure.TIM_ClockDivision = 0;
    TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit ( TIM2, &TIM_TimeBaseStructure );
    TIM_ITConfig ( TIM2, TIM_IT_Update | TIM_IT_Trigger, ENABLE );
    TIM_Cmd ( TIM2, ENABLE );
    NVIC_InitStructure.NVIC_IRQChannel = TIM2_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
}
​
void TIM3_IRQHandler ( void ) {
    if ( TIM_GetITStatus ( TIM3, TIM_IT_Update ) != RESET ) {
        /* user code */
    }
​
    TIM_ClearITPendingBit ( TIM3, TIM_IT_Update );
}
​
void Send_Timer_Init ( u16 arr, u16 psc ) {
    TIM_TimeBaseInitTypeDef  TIM_TimeBaseStructure;
    NVIC_InitTypeDef NVIC_InitStructure;
    RCC_APB1PeriphClockCmd ( RCC_APB1Periph_TIM2 | RCC_APB1Periph_TIM3, ENABLE );
    TIM_TimeBaseStructure.TIM_Period = arr;
    TIM_TimeBaseStructure.TIM_Prescaler = psc;
    TIM_TimeBaseStructure.TIM_ClockDivision = 0;
    TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit ( TIM3, &TIM_TimeBaseStructure );
    TIM_ITConfig ( TIM3, TIM_IT_Update | TIM_IT_Trigger, ENABLE );
    TIM_Cmd ( TIM3, ENABLE );
    NVIC_PriorityGroupConfig ( NVIC_PriorityGroup_4 );
    NVIC_InitStructure.NVIC_IRQChannel = TIM3_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 2;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init ( &NVIC_InitStructure );
}
```

注意，在使用时，首先调用`Send_Timer_Init`，然后调用`TIM2_Int_Init`。