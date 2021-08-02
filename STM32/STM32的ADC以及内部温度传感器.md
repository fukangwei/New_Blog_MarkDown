---
title: STM32的ADC以及内部温度传感器
categories: STM32
mathjax: true
date: 2019-03-19 09:58:57
---
### ADC基础

&emsp;&emsp;`STM32`有如下`ADC`转换模式：<!--more-->

- `单次`：对单个端口只转换一次。
- `连续`：对端口连续转换多次。
- `扫描`：对多个端口进行扫描。
- `中断`。

&emsp;&emsp;`STM32`的`ADC`分为注入组(`Injected Channel`)和规则组(`Regular Channel`)：

- 规则组放的是常规情况下的检查所需要的数据，可以说规则组是主程序。
- 注入组放的是特殊情况下的检查所需要的数据，可以说注入组是中断程序。

### ADC通道

&emsp;&emsp;`STM32`的`ADC`相关通道如下：

通道     | ADC1        | ADC2  | ADC3   | 通道     | ADC1        | ADC2  | ADC3
---------|-------------|-------|--------|---------|-------------|-------|------
通道`0`  | `PA0`       | `PA0` | `PA0`  | 通道`1`  | `PA1`       | `PA1` | `PA1`
通道`2`  | `PA2`       | `PA2` | `PA2`  | 通道`3`  | `PA3`       | `PA3` | `PA3`
通道`4`  | `PA4`       | `PA4` | `PF6`  | 通道`5`  | `PA5`       | `PA5` | `PF7`
通道`6`  | `PA6`       | `PA6` | `PF8`  | 通道`7`  | `PA7`       | `PA7` | `PF9`
通道`8`  | `PB0`       | `PB0` | `PF10` | 通道`9`  | `PB1`       | `PB1` |
通道`10` | `PC0`       | `PC0` | `PC0`  | 通道`11` | `PC1`       | `PC1` | `PC1`
通道`12` | `PC2`       | `PC2` | `PC2`  | 通道`13` | `PC3`       | `PC3` | `PC3`
通道`14` | `PC4`       | `PC4` |        | 通道`15` | `PC5`       | `PC5` |
通道`16` | 温度传感器   |       |        | 通道`17` | 内部参考电压 |       |

### 参考代码

&emsp;&emsp;以下是参考代码，使用`ADC1`的`IN0`脚：

``` cpp
void ADC_GPIO_Init ( void ) {
    GPIO_InitTypeDef GPIO_InitStructure;
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA | RCC_APB2Periph_ADC1, ENABLE );
    GPIO_DeInit ( GPIOA );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN; /* 设为模拟输入 */
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
}

void ADC_configuration ( void ) {
    ADC_InitTypeDef ADC_InitStructure;
    ADC_InitStructure.ADC_Mode = ADC_Mode_Independent; /* 独立模式 */
    ADC_InitStructure.ADC_ScanConvMode = DISABLE; /* 连续多通道模式 */
    ADC_InitStructure.ADC_ContinuousConvMode = DISABLE; /* 单次转换 */
    /* 转换由软件而不是外部触发启动 */
    ADC_InitStructure.ADC_ExternalTrigConv = ADC_ExternalTrigConv_None;
    ADC_InitStructure.ADC_DataAlign = ADC_DataAlign_Right; /* 右对齐 */
    ADC_InitStructure.ADC_NbrOfChannel = 1; /* 扫描通道数 */
    ADC_Init ( ADC1, &ADC_InitStructure );
    // ADC_RegularChannelConfig ( ADC1, ADC_Channel_0, 1, ADC_SampleTime_7Cycles5 );
    ADC_Cmd ( ADC1, ENABLE ); /* 使能或者失能指定的ADC */
    ADC_ResetCalibration ( ADC1 ); /* 重置指定的ADC的校准寄存器 */

    while ( ADC_GetResetCalibrationStatus ( ADC1 ) ); /* 等待校准寄存器初始化 */

    ADC_StartCalibration ( ADC1 ); /* 开始校准 */

    while ( ADC_GetCalibrationStatus ( ADC1 ) ); /* 等待校准完成 */

    ADC_SoftwareStartConvCmd ( ADC1, ENABLE ); /* 使能指定的ADC的软件转换启动功能 */
}

u16 GetADCValue ( u8 ADC_Channel ) { /* ADC_Channel_x 0~17 */
    u16 adc_value;
    ADC_RegularChannelConfig ( ADC1, ADC_Channel, 1, ADC_SampleTime_7Cycles5 );
    ADC_SoftwareStartConvCmd ( ADC1, ENABLE ); /* 使能指定的ADC的软件转换启动功能 */

    /* 检查制定ADC标志位是否置为1，ADC_FLAG_EOC是转换结束标志位 */
    while ( ADC_GetFlagStatus ( ADC1, ADC_FLAG_EOC ) == RESET );

    adc_value = ADC_GetConversionValue ( ADC1 );
    return adc_value; /* 返回最近一次ADCx规则组的转换结果 */
}
```

### 内部温度传感器

&emsp;&emsp;当使用内部温度传感器时，需要使能温度传感器通道：

``` cpp
ADC_TempSensorVrefintCmd ( ENABLE );
```

&emsp;&emsp;温度的计算公式为$\displaystyle{Temp = \frac{V_{25} - V_{SENSE}}{Avg\\_Slope} + 25}$：

- $V_{25}$是$V_{SENSE}$在$25^{\circ}C$时的数值。
- $Avg\\_Slope$是温度与$V_{SENSE}$曲线的平均斜率。

$V_{25}$的典型值为`1.43`，$Avg\\_Slope$的典型值为$4.3mV/^{\circ}C$，则$\displaystyle{Temp = \frac{1.43 - V_{SENSE}}{0.0043} + 25}$。