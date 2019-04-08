---
title: STM32的GPIO库函数
date: 2019-01-19 08:42:45
tags:
---
&emsp;&emsp;操作步骤如下所示：
&emsp;&emsp;1. 使能GPIO对应的外设时钟：

``` c
/* 使能GPIOA、GPIOB、GPIOC对应的外设时钟 */
RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA | RCC_APB2Periph_GPIOB | \
                         RCC_APB2Periph_GPIOC, ENABLE );
```

&emsp;&emsp;2. 声明一个`GPIO_InitStructure`结构体：

``` c
GPIO_InitTypeDef GPIO_InitStructure;
```

&emsp;&emsp;3. 选择待设置的`GPIO`管脚：

``` c
/* 选择待设置的GPIO第7、8、9管脚位，中间加“|”符号 */
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_7 | GPIO_Pin_8 | GPIO_Pin_9;
```

&emsp;&emsp;4. 设置选中`GPIO`管脚的速率：

``` c
/* 设置选中的GPIO管脚的最高速率为2MHz */
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_2MHz;
```

&emsp;&emsp;5. 设置选中`GPIO`管脚的模式：

``` c
/* 设置选中的GPIO管脚模式为开漏输出模式 */
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD;
```

&emsp;&emsp;6. 根据`GPIO_InitStructure`中指定的参数初始化外设`GPIOX`：

``` c
/* 根据GPIO_InitStructure中指定的参数初始化外设GPIOC */
GPIO_Init ( GPIOC, &GPIO_InitStructure );
```

&emsp;&emsp;7. 其他应用如下：

``` c
/* 将端口GPIOA的第10、15脚置1(高电平) */
GPIO_SetBits ( GPIOA, GPIO_Pin_10 | GPIO_Pin_15 );
/* 将端口GPIOA的第10、15脚置0(低电平) */
GPIO_ResetBits ( GPIOA, GPIO_Pin_10 | GPIO_Pin_15 );
```

&emsp;&emsp;`GPIO`寄存器如下：

寄存器    | 描述
---------|-----
`CRL`    | 端口配置低寄存器
`CRH`    | 端口配置高寄存器
`IDR`    | 端口输入数据寄存器
`ODR`    | 端口输出数据寄存器
`BSRR`   | 端口位设置/复位寄存器
`BRR`    | 端口位复位寄存器
`LCKR`   | 端口配置锁定寄存器
`EVCR`   | 事件控制寄存器
`MAPR`   | 复用重映射和调试
`I/O`    | 配置寄存器
`EXTICR` | 外部中断线路`0`至`15`配置寄存器

&emsp;&emsp;`GPIO`库函数如下：

函数名                    | 描述
-------------------------|-----
`GPIO_DeInit`            | 将外设`GPIOx`寄存器重设为缺省值
`GPIO_AFIODeInit`        | 将复用功能(重映射事件控制和`EXTI`设置)重设为缺省值
`GPIO_Init`              | 根据`GPIO_InitStruct`中指定的参数初始化外设`GPIOx`寄存器
`GPIO_StructInit`        | 把`GPIO_InitStruct`中的每一个参数按缺省值填入
`GPIO_ReadInputDataBit`  | 读取指定端口管脚的输入
`GPIO_ReadInputData`     | 读取指定的`GPIO`端口输入
`GPIO_ReadOutputDataBit` | 读取指定端口管脚的输出
`GPIO_ReadOutputData`    | 读取指定的`GPIO`端口输出
`GPIO_SetBits`           | 设置指定的数据端口位
`GPIO_ResetBits`         | 清除指定的数据端口位
`GPIO_WriteBit`          | 设置或者清除指定的数据端口位
`GPIO_Write`             | 向指定`GPIO`数据端口写入数据
`GPIO_PinLockConfig`     | 锁定`GPIO`管脚设置寄存器
`GPIO_EventOutputConfig` | 选择`GPIO`管脚用作事件输出
`GPIO_EventOutputCmd`    | 使能或者失能事件输出
`GPIO_PinRemapConfig`    | 改变指定管脚的映射
`GPIO_EXTILineConfig`    | 选择`GPIO`管脚用作外部中断线路

&emsp;&emsp;`GPIO_DeInit`：将外设`GPIOx`寄存器重设为缺省值。

``` c
GPIO_DeInit ( GPIOA );
```

&emsp;&emsp;`GPIO_AFIODeInit`：将复用功能(重映射事件控制和`EXTI`设置)重设为缺省值。

``` c
GPIO_AFIODeInit();
```

&emsp;&emsp;`GPIO_Init`：根据`GPIO_InitStruct`中指定的参数初始化外设`GPIOx`寄存器。

``` c
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Pin   = GPIO_Pin_All;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_10MHz;
GPIO_InitStructure.GPIO_Mode  = GPIO_Mode_IN_FLOATING;
GPIO_Init ( GPIOA, &GPIO_InitStructure );
```

`GPIO_InitTypeDef`定义在`stm32f10x_gpio.h`：

``` c
typedef struct {
    u16 GPIO_Pin;
    GPIOSpeed_TypeDef GPIO_Speed;
    GPIOMode_TypeDef GPIO_Mode;
} GPIO_InitTypeDef;
```

- `GPIO_Pin`：该参数选择待设置的`GPIO`管脚，使用操作符`|`可以一次选中多个管脚。可以使用下表中的任意组合：

1. `GPIO_Pin_None`：无管脚被选中。
2. `GPIO_Pin_x`：选中管脚`x`(`0`至`15`)。
3. `GPIO_Pin_All`：选中全部管脚。

- `GPIO_Speed`：用以设置选中管脚的速率：

1. `GPIO_Speed_10MHz`：最高输出速率`10MHz`。
2. `GPIO_Speed_2MHz`：最高输出速率`2MHz`。
3. `GPIO_Speed_50MHz`：最高输出速率`50MHz`。

- `GPIO_Mode`：用以设置选中管脚的工作状态：

1. `GPIO_Mode_AIN`：模拟输入。
2. `GPIO_Mode_IN_FLOATING`：浮空输入。
3. `GPIO_Mode_IPD`：下拉输入。
4. `GPIO_Mode_IPU`：上拉输入。
5. `GPIO_Mode_Out_OD`：开漏输出。
6. `GPIO_Mode_Out_PP`：推挽输出。
7. `GPIO_Mode_AF_OD`：复用开漏输出。
8. `GPIO_Mode_AF_PP`：复用推挽输出。

&emsp;&emsp;`GPIO_StructInit`：把`GPIO_InitStruct`中的每一个参数按缺省值填入。

``` c
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_StructInit ( &GPIO_InitStructure );
```

&emsp;&emsp;`GPIO_ReadInputDataBit`：读取指定端口管脚的输入。

``` c
u8 ReadValue = GPIO_ReadInputDataBit ( GPIOB, GPIO_Pin_7 );
```

&emsp;&emsp;`GPIO_ReadInputData`：读取指定的`GPIO`端口输入。

``` c
u16 ReadValue = GPIO_ReadInputData ( GPIOC );
```

&emsp;&emsp;`GPIO_ReadOutputDataBit`：读取指定端口管脚的输出。

``` c
u8 ReadValue = GPIO_ReadOutputDataBit ( GPIOB, GPIO_Pin_7 );
```

&emsp;&emsp;`GPIO_ReadOutputData`：读取指定的`GPIO`端口输出。

``` c
u16 ReadValue = GPIO_ReadOutputData ( GPIOC );
```

&emsp;&emsp;`GPIO_SetBits`：置位指定的数据端口位。

``` c
/* 将端口GPIOA的第10、15脚置1(高电平) */
GPIO_SetBits ( GPIOA, GPIO_Pin_10 | GPIO_Pin_15 );
```

&emsp;&emsp;`GPIO_ResetBits`：清除指定的数据端口位。

``` c
/* 将端口GPIOA的第10、15脚置0(低电平) */
GPIO_ResetBits ( GPIOA, GPIO_Pin_10 | GPIO_Pin_15 );
```

&emsp;&emsp;`GPIO_WriteBit`：设置或者清除指定的数据端口位。

``` c
GPIO_WriteBit ( GPIOA, GPIO_Pin_15, Bit_SET );
```

&emsp;&emsp;`GPIO_Write`：向指定`GPIO`数据端口写入数据。

``` c
GPIO_Write ( GPIOA, 0x1101 );
```

&emsp;&emsp;`GPIO_PinLockConfig`：锁定`GPIO`管脚设置寄存器。

``` c
GPIO_PinLockConfig ( GPIOA, GPIO_Pin_0 | GPIO_Pin_1 );
```

&emsp;&emsp;`GPIO_EventOutputConfig`：选择`GPIO`管脚用作事件输出。

``` c
GPIO_EventOutputConfig ( GPIO_PortSourceGPIOE, GPIO_PinSource5 );
```

`GPIO_PortSource`用以选择用作事件输出的`GPIO`端口。
&emsp;&emsp;`GPIO_EventOutputCmd`：使能或者失能事件输出。

``` c
GPIO_EventOutputConfig ( GPIO_PortSourceGPIOC, GPIO_PinSource6 );
GPIO_EventOutputCmd ( ENABLE );
```

&emsp;&emsp;`GPIO_PinRemapConfig`：改变指定管脚的映射。

``` c
GPIO_PinRemapConfig ( GPIO_Remap_I2C1, ENABLE );
```