---
title: STM32的ADC多通道采样
categories: 单片机
abbrlink: 57a48f94
date: 2018-12-29 18:22:01
---
&emsp;&emsp;用`ADC`连续采集`11`路模拟信号，并由`DMA`传输到内存。`ADC`配置为扫描并且连续转换模式，`ADC`的时钟配置为`12MHz`。在每次转换结束后，由`DMA`循环将转换的数据传输到内存中。`ADC`可以连续采集`N`次并求平均值，最后通过串口传输出最后转换的结果。

``` c
#include "stm32f10x.h"
#include "eval.h"
#include "SysTickDelay.h"
#include "UART_INTERFACE.h"
#include <stdio.h>
​
#define N 50 /* 每通道采50次 */
#define M 12 /* 为12个通道 */
​
vu16 AD_Value[N][M]; /* 用来存放ADC转换结果，也是DMA的目标地址 */
vu16 After_filter[M]; /* 用来存放求平均值之后的结果 */
​
void GPIO_Configuration ( void ) {
    GPIO_InitTypeDef GPIO_InitStructure;
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
    /* 因为USART1管脚是以复用的形式接到GPIO口上的，所以使用复用推挽式输出 */
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    /* PA0/1/2 作为模拟通道输入引脚 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1 | GPIO_Pin_2 | GPIO_Pin_3;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN; /* 模拟输入引脚 */
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    /* PB0/1 作为模拟通道输入引脚 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN; /* 模拟输入引脚 */
    GPIO_Init ( GPIOB, &GPIO_InitStructure );
    /* PC0/1/2/3/4/5 作为模拟通道输入引脚 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1 | GPIO_Pin_2 |
                                  GPIO_Pin_3 | GPIO_Pin_4 | GPIO_Pin_5;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN; /* 模拟输入引脚 */
    GPIO_Init ( GPIOC, &GPIO_InitStructure );
}
​
void RCC_Configuration ( void ) {
    ErrorStatus HSEStartUpStatus;
    RCC_DeInit(); /* RCC系统复位 */
    RCC_HSEConfig ( RCC_HSE_ON ); /* 开启HSE */
    HSEStartUpStatus = RCC_WaitForHSEStartUp(); /* 等待HSE准备好 */
​
    if ( HSEStartUpStatus == SUCCESS ) {
        FLASH_PrefetchBufferCmd ( FLASH_PrefetchBuffer_Enable ); /* Enable Prefetch Buffer */
        FLASH_SetLatency ( FLASH_Latency_2 ); /* Set 2 Latency cycles */
        RCC_HCLKConfig ( RCC_SYSCLK_Div1 ); /* AHB clock = SYSCLK */
        RCC_PCLK2Config ( RCC_HCLK_Div1 ); /* APB2 clock = HCLK */
        RCC_PCLK1Config ( RCC_HCLK_Div2 ); /* APB1 clock = HCLK/2 */
        RCC_PLLConfig ( RCC_PLLSource_HSE_Div1, RCC_PLLMul_6 ); /* PLLCLK = 12MHz * 6 = 72 MHz */
        RCC_PLLCmd ( ENABLE ); /* Enable PLL */
​
        while ( RCC_GetFlagStatus ( RCC_FLAG_PLLRDY ) == RESET ); /* Wait till PLL is ready */
​
        RCC_SYSCLKConfig ( RCC_SYSCLKSource_PLLCLK ); /* Select PLL as system clock source */
​
        while ( RCC_GetSYSCLKSource() != 0x08 ); /* Wait till PLL is used as system clock source */
​        /* 使能ADC1通道时钟，各个管脚时钟 */
        RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA | RCC_APB2Periph_GPIOB
                                 | RCC_APB2Periph_GPIOC | RCC_APB2Periph_ADC1
                                 | RCC_APB2Periph_AFIO | RCC_APB2Periph_USART1, ENABLE );
        RCC_ADCCLKConfig ( RCC_PCLK2_Div6 ); /* 72M/6 = 12，ADC最大时间不能超过14M */
        RCC_AHBPeriphClockCmd ( RCC_AHBPeriph_DMA1, ENABLE ); /* 使能DMA传输 */
    }
}
​
void ADC1_Configuration ( void ) {
    ADC_InitTypeDef ADC_InitStructure;
    ADC_DeInit ( ADC1 ); /* 将外设ADC1的全部寄存器重设为缺省值 */
    ADC_InitStructure.ADC_Mode = ADC_Mode_Independent; /* ADC工作模式：ADC1和ADC2工作在独立模式 */
    ADC_InitStructure.ADC_ScanConvMode = ENABLE; /* 模数转换工作在扫描模式 */
    ADC_InitStructure.ADC_ContinuousConvMode = ENABLE; /* 模数转换工作在连续转换模式 */
    ADC_InitStructure.ADC_ExternalTrigConv = ADC_ExternalTrigConv_None; /* 外部触发转换关闭 */
    ADC_InitStructure.ADC_DataAlign = ADC_DataAlign_Right; /* ADC数据右对齐 */
    ADC_InitStructure.ADC_NbrOfChannel = M; /* 顺序进行规则转换的ADC通道的数目 */
    ADC_Init ( ADC1, &ADC_InitStructure ); /* 根据ADC_InitStruct中指定的参数初始化外设ADCx的寄存器 */
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_0, 1, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_1, 2, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_2, 3, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_3, 4, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_8, 5, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_9, 6, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_10, 7, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_11, 8, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_12, 9, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_13, 10, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_14, 11, ADC_SampleTime_239Cycles5 );
    ADC_RegularChannelConfig ( ADC1, ADC_Channel_15, 12, ADC_SampleTime_239Cycles5 );
    ADC_DMACmd ( ADC1, ENABLE ); /* 开启ADC的DMA支持(要实现DMA功能，还需独立配置DMA通道等参数) */
    ADC_Cmd ( ADC1, ENABLE ); /* 使能指定的ADC1 */
    ADC_ResetCalibration ( ADC1 ); /* 复位指定的ADC1的校准寄存器 */
​
    while ( ADC_GetResetCalibrationStatus ( ADC1 ) ); /* 获取ADC1复位校准寄存器的状态,设置状态则等待 */
    ADC_StartCalibration ( ADC1 ); /* 开始指定ADC1的校准状态 */
    while ( ADC_GetCalibrationStatus ( ADC1 ) ); /* 获取指定ADC1的校准程序，设置状态则等待 */
}
​
void DMA_Configuration ( void ) {
    DMA_InitTypeDef DMA_InitStructure;
    DMA_DeInit ( DMA1_Channel1 ); /* 将DMA的通道1寄存器重设为缺省值 */
    DMA_InitStructure.DMA_PeripheralBaseAddr = ( u32 ) &ADC1->DR; /* DMA外设ADC基地址 */
    DMA_InitStructure.DMA_MemoryBaseAddr = ( u32 ) &AD_Value; /* DMA内存基地址 */
    DMA_InitStructure.DMA_DIR = DMA_DIR_PeripheralSRC; /* 内存作为数据传输的目的地 */
    DMA_InitStructure.DMA_BufferSize = N * M; /* DMA通道的DMA缓存的大小 */
    DMA_InitStructure.DMA_PeripheralInc = DMA_PeripheralInc_Disable; /* 外设地址寄存器不变 */
    DMA_InitStructure.DMA_MemoryInc = DMA_MemoryInc_Enable; /* 内存地址寄存器递增 */
    DMA_InitStructure.DMA_PeripheralDataSize = DMA_PeripheralDataSize_HalfWord; /* 数据宽度为16位 */
    DMA_InitStructure.DMA_MemoryDataSize = DMA_MemoryDataSize_HalfWord; /* 数据宽度为16位 */
    DMA_InitStructure.DMA_Mode = DMA_Mode_Circular; /* 工作在循环缓存模式 */
    DMA_InitStructure.DMA_Priority = DMA_Priority_High; /* DMA通道x拥有高优先级 */
    DMA_InitStructure.DMA_M2M = DMA_M2M_Disable; /* DMA通道x没有设置为内存到内存传输 */
    /* 根据DMA_InitStruct中指定的参数，初始化DMA的通道 */
    DMA_Init ( DMA1_Channel1, &DMA_InitStructure );
}
​
void Init_All_Periph ( void ) { /* 配置所有外设 */
    RCC_Configuration();
    GPIO_Configuration();
    ADC1_Configuration();
    DMA_Configuration();
    USART_Configuration ( 9600 );
}
​
u16 GetVolt ( u16 advalue ) {
    return ( u16 ) ( advalue * 330 / 4096 ); /* 将求出的结果扩大了100倍，方便下面求出小数 */
}
​
void filter ( void ) {
    int sum = 0;
    u8 count;
    int i = 0;
​
    for ( i = 0; i < 12; i++ ) {
        for ( count = 0; count < N; count++ ) {
            sum += AD_Value[count][i];
        }
​
        After_filter[i] = sum / N;
        sum = 0;
    }
}
​
int main ( void ) {
    u16 value[M];
    int i = 0;
    init_All_Periph();
    SysTick_Initaize();
    ADC_SoftwareStartConvCmd ( ADC1, ENABLE );
    DMA_Cmd ( DMA1_Channel1, ENABLE ); /* 启动DMA通道 */
​
    while ( 1 ) {
        /* 等待传输完成，否则第一位数据容易丢失 */
        while ( USART_GetFlagStatus ( USART1, USART_FLAG_TXE ) == RESET );
​
        filter();
​
        for ( i = 0; i < 12; i++ ) {
            value[i] = GetVolt ( After_filter[i] );
            printf ( "value[%d]:\t%d.%dv\n", i, value[i] / 100, value[i]0 );
            delay_ms ( 100 );
        }
    }
}
```

&emsp;&emsp;该程序中的两个宏定义`M`和`N`，分别代表通道数、每个通道转换次数。将转换值由二进制转换为十进制时，要先扩大`100`倍，方便显示小数。在串口输出时，在`printf`语句之前加这句代码，防止输出的第一位数据丢失：

``` C
while ( USART_GetFlagStatus ( USART1, USART_FLAG_TXE ) == RESET );
```