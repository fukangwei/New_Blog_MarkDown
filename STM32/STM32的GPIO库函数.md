---
title: STM32的GPIO库函数
categories: STM32
date: 2019-01-19 08:42:45
---
### 操作步骤

&emsp;&emsp;`GPIO`的操作步骤如下：<!--more-->
&emsp;&emsp;1. 使能`GPIO`对应的外设时钟：

``` cpp
/* 使能GPIOA、GPIOB、GPIOC对应的外设时钟 */
RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA | RCC_APB2Periph_GPIOB | \
                         RCC_APB2Periph_GPIOC, ENABLE );
```

&emsp;&emsp;2. 声明一个`GPIO_InitStructure`结构体：

``` cpp
GPIO_InitTypeDef GPIO_InitStructure;
```

&emsp;&emsp;3. 选择`GPIO`管脚：

``` cpp
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_7 | GPIO_Pin_8 | GPIO_Pin_9; /* 选择GPIO的第7、8、9管脚 */
```

&emsp;&emsp;4. 设置`GPIO`管脚的速率：

``` cpp
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_2MHz; /* 设置GPIO管脚的最高速率为2MHz */
```

&emsp;&emsp;5. 设置`GPIO`管脚的模式：

``` cpp
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD; /* 设置GPIO管脚模式为开漏输出模式 */
```

&emsp;&emsp;6. 根据`GPIO_InitStructure`中指定的参数初始化外设`GPIO`：

``` cpp
GPIO_Init ( GPIOC, &GPIO_InitStructure ); /* 初始化外设GPIOC */
```

### GPIO_DeInit

&emsp;&emsp;将外设`GPIOx`寄存器重设为缺省值：

``` cpp
GPIO_DeInit ( GPIOA );
```

### GPIO_AFIODeInit

&emsp;&emsp;将复用功能重设为缺省值：

``` cpp
GPIO_AFIODeInit();
```

### GPIO_Init

&emsp;&emsp;根据`GPIO_InitStruct`中指定的参数初始化外设`GPIOx`寄存器：

``` cpp
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Pin   = GPIO_Pin_All;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_10MHz;
GPIO_InitStructure.GPIO_Mode  = GPIO_Mode_IN_FLOATING;
GPIO_Init ( GPIOA, &GPIO_InitStructure );
```

&emsp;&emsp;`GPIO_InitTypeDef`定义在`stm32f10x_gpio.h`：

``` cpp
typedef struct {
    u16 GPIO_Pin;
    GPIOSpeed_TypeDef GPIO_Speed;
    GPIOMode_TypeDef GPIO_Mode;
} GPIO_InitTypeDef;
```

- `GPIO_Pin`：选择待设置的`GPIO`管脚，可以使用以下任意组合：

1. `GPIO_Pin_None`：无管脚被选中。
2. `GPIO_Pin_x`：选中管脚`x`(`0`至`15`)。
3. `GPIO_Pin_All`：选中全部管脚。

- `GPIO_Speed`：设置选中管脚的速率：

1. `GPIO_Speed_10MHz`：最高输出速率`10MHz`。
2. `GPIO_Speed_2MHz`：最高输出速率`2MHz`。
3. `GPIO_Speed_50MHz`：最高输出速率`50MHz`。

- `GPIO_Mode`：设置选中管脚的工作状态：

GPIO_Mode          | 工作状态     | GPIO_Mode               | 工作状态
-------------------|-------------|-------------------------|--------
`GPIO_Mode_AIN`    | 模拟输入     | `GPIO_Mode_IN_FLOATING` | 浮空输入
`GPIO_Mode_IPD`    | 下拉输入     | `GPIO_Mode_IPU`         | 上拉输入
`GPIO_Mode_Out_OD` | 开漏输出     | `GPIO_Mode_Out_PP`      | 推挽输出
`GPIO_Mode_AF_OD`  | 复用开漏输出 | `GPIO_Mode_AF_PP`       | 复用推挽输出

### GPIO_StructInit

&emsp;&emsp;把`GPIO_InitStruct`中的每一个参数按缺省值填入：

``` cpp
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_StructInit ( &GPIO_InitStructure );
```

### GPIO_ReadInputDataBit

&emsp;&emsp;读取指定端口管脚的输入：

``` cpp
u8 ReadValue = GPIO_ReadInputDataBit ( GPIOB, GPIO_Pin_7 );
```

### GPIO_ReadInputData

&emsp;&emsp;读取指定的`GPIO`端口输入：

``` cpp
u16 ReadValue = GPIO_ReadInputData ( GPIOC );
```

### GPIO_ReadOutputDataBit

&emsp;&emsp;读取指定`GPIO`端口和管脚的输出：

``` cpp
u8 ReadValue = GPIO_ReadOutputDataBit ( GPIOB, GPIO_Pin_7 );
```

### GPIO_ReadOutputData

&emsp;&emsp;读取指定的`GPIO`端口输出：

``` cpp
u16 ReadValue = GPIO_ReadOutputData ( GPIOC );
```

### GPIO_SetBits

&emsp;&emsp;置位指定的数据端口位：

``` cpp
GPIO_SetBits ( GPIOA, GPIO_Pin_10 | GPIO_Pin_15 ); /* 将端口GPIOA的第10、15脚置1，即高电平 */
```

### GPIO_ResetBits

&emsp;&emsp;清除指定的数据端口位：

``` cpp
GPIO_ResetBits ( GPIOA, GPIO_Pin_10 | GPIO_Pin_15 ); /* 将端口GPIOA的第10、15脚置0，即低电平 */
```

### GPIO_WriteBit

&emsp;&emsp;设置或清除指定的数据端口位：

``` cpp
GPIO_WriteBit ( GPIOA, GPIO_Pin_15, Bit_SET );
```

### GPIO_Write

&emsp;&emsp;向指定`GPIO`端口写入数据：

``` cpp
GPIO_Write ( GPIOA, 0x1101 );
```

### GPIO_PinRemapConfig

&emsp;&emsp;改变指定管脚的映射：

``` cpp
GPIO_PinRemapConfig ( GPIO_Remap_I2C1, ENABLE );
```