---
title: 刘凯STM32笔记
date: 2019-01-18 19:27:18
categories: 单片机
---
### 定时器

&emsp;&emsp;代码如下：

``` c
void TIM_Configuration ( void ) {
    TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure; /* 定义结构体变量 */
    TIM_OCInitTypeDef TIM_OCInitStructure;
    RCC_APB1PeriphClockCmd ( RCC_APB1Periph_TIM2, ENABLE ); /* 开启定时器2时钟 */
    TIM_DeInit ( TIM2 ); /* 将外设TIMx寄存器重设为缺省值 */
    TIM_InternalClockConfig ( TIM2 ); /* 设置TIMx内部时钟 */
    /* TIM_Period设置了在下一个更新事件装入活动的自动重装载寄存器周期的值 */
    TIM_TimeBaseStructure.TIM_Period = 2000;
    /* TIM_Prescaler设置了用来作为TIMx时钟频率除数的预分频值。时钟的分频值为35999，
       则时钟分频36000；时钟的分频值为0，则时钟分频1 */
    /* PSC是用来设置分频的，系统时钟是72M，这里就是分频值，你设置7199就是7200分频，
       那么定时器计数频率就是10KHz了(72M / 7200 = 10k) */
    TIM_TimeBaseStructure.TIM_Prescaler = 35999;
    /* TIM_ClockDivision设置了时钟分割 */
    TIM_TimeBaseStructure.TIM_ClockDivision = 0x0;
    /* TIM_CounterMode选择了计数器模式：向上计数 */
    TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up;
    /* 根据TIM_TimeBaseInitStruct中指定的参数初始化TIMx的时间基数单位 */
    TIM_TimeBaseInit ( TIM2, &TIM_TimeBaseStructure );
    /* TIM_OCMode选择定时器模式(在这个程序中可有可无)：TIM输出比较时间模式 */
    TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_Timing;
    /* 根据TIM_OCInitStruct中指定的参数初始化外设TIMx */
    TIM_OC1Init ( TIM2, &TIM_OCInitStructure );
    TIM_Cmd ( TIM2, ENABLE ); /* 使能TIM2 */
    /* 设置TIMx预分频(TIM预分频值即时装入) */
    TIM_PrescalerConfig ( TIM2, 35999, TIM_PSCReloadMode_Immediate );
    /* 清除TIMx的待处理标志位(TIM更新标志位) */
    TIM_ClearFlag ( TIM2, TIM_FLAG_Update );
    /* 使能或者失能指定的TIM中断(TIM中断源) */
    TIM_ITConfig ( TIM2, TIM_IT_Update, ENABLE );
}
```

### 数码管

&emsp;&emsp;代码如下：

``` c
void SMG_Init ( void ) {
    GPIO_InitTypeDef GPIO_InitStructure;
    SPI_74HC595_Init();
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOB, ENABLE );
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOC, ENABLE );
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOE, ENABLE );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init ( GPIOE, &GPIO_InitStructure );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8 | GPIO_Pin_9;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init ( GPIOC, &GPIO_InitStructure );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_15;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init ( GPIOB, &GPIO_InitStructure );
}
​
void SPI_74HC595_Init ( void ) {
    GPIO_InitTypeDef GPIO_InitStructure;
    SPI_InitTypeDef SPI_InitStructure;
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOA, ENABLE );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5 | GPIO_Pin_6 | GPIO_Pin_7;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_Init ( GPIOA, &GPIO_InitStructure );
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_SPI1, ENABLE );
    SPI_Cmd ( SPI1, DISABLE );
    SPI_InitStructure.SPI_Direction = SPI_Direction_2Lines_FullDuplex;
    SPI_InitStructure.SPI_Mode = SPI_Mode_Master;
    SPI_InitStructure.SPI_DataSize = SPI_DataSize_8b;
    SPI_InitStructure.SPI_CPOL = SPI_CPOL_High;
    SPI_InitStructure.SPI_CPHA = SPI_CPHA_2Edge;
    SPI_InitStructure.SPI_NSS = SPI_NSS_Soft;
    SPI_InitStructure.SPI_BaudRatePrescaler = SPI_BaudRatePrescaler_4;
    SPI_InitStructure.SPI_FirstBit = SPI_FirstBit_MSB;
    SPI_InitStructure.SPI_CRCPolynomial = 7;
    SPI_Init ( SPI1, &SPI_InitStructure );
    SPI_Cmd ( SPI1, ENABLE );
    GPIO_ResetBits ( GPIOA, GPIO_Pin_0 );
}
```

### IIC总线

&emsp;&emsp;`I2C_BufferWrite`函数如下：

``` c
void I2C_BufferWrite ( u8 *pBuffer, u8 WriteAddr, u16 NumByteToWrite ) {
    u8 NumOfPage = 0, NumOfSingle = 0, Addr = 0, count = 0;
    Addr = WriteAddr % I2C_PageSize;
    count = I2C_PageSize - Addr;
    NumOfPage = NumByteToWrite / I2C_PageSize;
    NumOfSingle = NumByteToWrite % I2C_PageSize;
    I2C_WaitEepromStandbyState(); /* 等EEPROM处于空闲状态 */
​
    if ( Addr == 0 ) { /* If WriteAddr is I2C_PageSize aligned */
        /* If NumByteToWrite < I2C_PageSize */
        if ( NumOfPage == 0 ) { /* 只有1页的情况下 */
            /* 对页进行写操作 */
            I2C_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
            I2C_WaitEepromStandbyState();
        } else { /* If NumByteToWrite > I2C_PageSize */
            while ( NumOfPage-- ) { /* 否则一直对页进行写操作 */
                I2C_PageWrite ( pBuffer, WriteAddr, I2C_PageSize );
                I2C_WaitEepromStandbyState();
                WriteAddr +=  I2C_PageSize;
                pBuffer += I2C_PageSize;
            }
​
            if ( NumOfSingle != 0 ) { /* 如果不够1页，也单独写一下 */
                I2C_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
                I2C_WaitEepromStandbyState();
            }
        }
    } else { /* If WriteAddr is not I2C_PageSize aligned */
        if ( NumOfPage == 0 ) { /* If NumByteToWrite < I2C_PageSize */
            I2C_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
            I2C_WaitEepromStandbyState();
        } else { /* If NumByteToWrite > I2C_PageSize */
            NumByteToWrite -= count;
            NumOfPage =  NumByteToWrite / I2C_PageSize;
            NumOfSingle = NumByteToWrite % I2C_PageSize;
​
            if ( count != 0 ) {
                I2C_PageWrite ( pBuffer, WriteAddr, count );
                I2C_WaitEepromStandbyState();
                WriteAddr += count;
                pBuffer += count;
            }
​
            while ( NumOfPage-- ) {
                I2C_PageWrite ( pBuffer, WriteAddr, I2C_PageSize );
                I2C_WaitEepromStandbyState();
                WriteAddr +=  I2C_PageSize;
                pBuffer += I2C_PageSize;
            }
​
            if ( NumOfSingle != 0 ) {
                I2C_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
                I2C_WaitEepromStandbyState();
            }
        }
    }
}
```

`*pBuffer`是需要写的内容(数据)，`WriteAddr`是需要写入数据的地址，`NumByteToWrite`是需要写多少个数据，它可以设置很长。

``` c
Addr = WriteAddr % I2C_PageSize; /* 得到WriteAddr(起始地址)在某一页中的的地址 */
```

`16`字节页缓冲器一次只能写`16`个字节，首先要计算要写多少页。当`16`字节页缓冲器将`16`字节的数据全写入`ROM`中去，下一页才开始写。
&emsp;&emsp;`I2C_WaitEepromStandbyState`函数如下：

``` c
void I2C_WaitEepromStandbyState ( void ) {
    vu16 SR1_Tmp = 0;
​
    do {
        /* Send START condition，发送开始信号 */
        I2C_GenerateSTART ( I2C2, ENABLE );
        /* Read I2C1 SR1 register */
        /* 读寄存器I2C_SR1，这个读操作实际上是让SR的某些标志位清零 */
        SR1_Tmp = I2C_ReadRegister ( I2C2, I2C_Register_SR1 );
        /* Send EEPROM address for write */
        I2C_Send7bitAddress ( I2C2, EEPROM_ADDRESS, I2C_Direction_Transmitter );
        /* 读寄存器I2C_SR1的ADDR位。如果ADDR为0，则地址发送没有结束，继续发送“EEPROM address” */
    } while ( ! ( I2C_ReadRegister ( I2C2, I2C_Register_SR1 ) & 0x0002 ) );
​
    I2C_ClearFlag ( I2C2, I2C_FLAG_AF ); /* Clear AF flag，清除应答错误标志位 */
}
```

&emsp;&emsp;`I2C_PageWrite`函数如下：

``` c
void I2C_PageWrite ( u8 *pBuffer, u8 WriteAddr, u8 NumByteToWrite ) { /* 页写操作 */
    I2C_WaitEepromStandbyState();
    I2C_GenerateSTART ( I2C2, ENABLE ); /* [1]Send START condition 发送起始信号 */
​
    /* [2]Test on EV5 and clear it 起始信号已发送并清除该事件 */
    /* 检查起始信号是否已经发送，如果没有完成，则程序保持I2C_EVENT_MASTER_MODE_SELECT状态 */
    while ( !I2C_CheckEvent ( I2C2, I2C_EVENT_MASTER_MODE_SELECT /* 主模式下的选择 */ ) );
​
    /* [3]Send EEPROM address for write 发送器件地址 */
    /* EEPROM_ADDRESS是EEPROM器件的地址；EEPROM有片内存储空间，存储空间又有存储地址WriteAddr */
    I2C_Send7bitAddress ( I2C2, EEPROM_ADDRESS, I2C_Direction_Transmitter );
​
    /* [4]Test on EV6 and clear it */
    /* 地址发送完成 */
    while ( !I2C_CheckEvent ( I2C2, I2C_EVENT_MASTER_TRANSMITTER_MODE_SELECTED ) );
​
    /* [5]Send EEPROM'S internal address to write to */
    I2C_SendData ( I2C2, WriteAddr ); /* 发送器件内部写入地址 */
​
    /* [6]Test on EV8 and clear it */
    /* 等待移位寄存器是否为空 */
    while ( !I2C_CheckEvent ( I2C2, I2C_EVENT_MASTER_BYTE_TRANSMITTED ) );
​
    /* [7]Send data to Written */
    while ( NumByteToWrite-- ) { /* 要写的字节数 */
        /* Send the current byte */
        /* 发送当前一个字节。注意u8是char型，一个字节；在1页中，
           EEPROM中的WriteAddr具有自增功能，不需要写“WriteAddr++” */
        I2C_SendData ( I2C2, *pBuffer );
        pBuffer++; /* Point to the next byte to be written */
        /*Test on EV8 and clear it */
        /* 等待发送缓冲区是否为空 */
        while ( !I2C_CheckEvent ( I2C2, I2C_EVENT_MASTER_BYTE_TRANSMITTED ) );
    }
​
    /* 写完1页的数据 */
    I2C_GenerateSTOP ( I2C2, ENABLE ); /* [8]Send STOP condition */
}
```

&emsp;&emsp;`I2C_EE_SequentialWrite`函数如下：

``` c
void I2C_EE_SequentialWrite ( u8 *pBuffer, u8 WriteAddr, u16 NumByteToWrite ) {
    u8 NumOfPage = 0; /* 页数 */
    u8 NumOfSingle = 0; /* 不足一页字节数 */
    u8 Addr = 0; /* 字节地址 */
    u8 count = 0; /* 字节计数器 */
    Addr = WriteAddr % I2C_PageSize; /* 字节地址(不到一页) */
    count = I2C_PageSize - Addr; /* 一页中的剩余字节数 */
    NumOfPage = NumByteToWrite / I2C_PageSize; /* 欲写入数据所占的页数 */
    /* 欲写入数据所占的字节数(不足一页) */
    NumOfSingle = NumByteToWrite % I2C_PageSize;
​
    /* If WriteAddr is I2C_PageSize aligned */
    if ( Addr == 0 ) { /* 起始地址为某页的第一个字节地址处 */
        /* If NumByteToWrite < I2C_PageSize */
        if ( NumOfPage == 0 ) { /* 写入的数据字节数不足一页 */
            I2C_EE_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
            I2C_EE_WaitEepromStandbyState(); /* 等待EEPROM自编程结束 */
        } else { /* If NumByteToWrite > I2C_PageSize */
            while ( NumOfPage-- ) { /* 写入的数据超过一页 */
                I2C_EE_PageWrite ( pBuffer, WriteAddr, I2C_PageSize );
                I2C_EE_WaitEepromStandbyState();
                WriteAddr += I2C_PageSize; /* 下一页起始地址 */
                pBuffer += I2C_PageSize;
            }
​
            if ( NumOfSingle != 0 ) { /* 写入余下不足一页的数据 */
                I2C_EE_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
                I2C_EE_WaitEepromStandbyState();
            }
        }
    } else { /* If WriteAddr is not I2C_PageSize aligned 写入数据的起始地址不在页的开始处 */
        /* If NumByteToWrite < I2C_PageSize */
        /* 不足一页的，直接写入。注意：当写到页边界时，将从该页的起始地址继续写入数据 */
        if ( NumOfPage == 0 ) {
            I2C_EE_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
            I2C_EE_WaitEepromStandbyState();
        } else { /* If NumByteToWrite > I2C_PageSize */
            NumByteToWrite -= count;
            NumOfPage = NumByteToWrite / I2C_PageSize;
            NumOfSingle = NumByteToWrite % I2C_PageSize;
​
            if ( count != 0 ) {
                /* 将WriteAddr所在页的剩余空间写满 */
                I2C_EE_PageWrite ( pBuffer, WriteAddr, count );
                I2C_EE_WaitEepromStandbyState();
                WriteAddr += count; /* 地址指向下一页的起始处 */
                pBuffer += count;
            }
​
            while ( NumOfPage-- ) {
                I2C_EE_PageWrite ( pBuffer, WriteAddr, I2C_PageSize );
                I2C_EE_WaitEepromStandbyState();
                WriteAddr += I2C_PageSize;
                pBuffer += I2C_PageSize;
            }
​
            if ( NumOfSingle != 0 ) {
                /* 写入余下不足一页的数据 */
                I2C_EE_PageWrite ( pBuffer, WriteAddr, NumOfSingle );
                I2C_EE_WaitEepromStandbyState();
            }
        }
    }
}
```

### STM32的RTC与RCC

&emsp;&emsp;`RCC`是`STM32`的时钟控制器，可开启或关闭各总线的时钟。在使用各外设功能前必须先开启其对应的时钟！没有这个时钟，内部的各器件就不能运行。而`RTC`是`STM32`内部集成的一个简单的时钟(计时用)，如果不用就关闭。用的话先要通过`RCC`配置其时钟源，可看作是一个外设器件。
&emsp;&emsp;`RCC_Configuration`函数如下：

``` c
void RCC_Configuration ( void ) {
    ErrorStatus HSEStartUpStatus;
    RCC_DeInit();
    RCC_HSEConfig ( RCC_HSE_ON );
    HSEStartUpStatus = RCC_WaitForHSEStartUp();
​
    if ( HSEStartUpStatus == SUCCESS ) {
        RCC_HCLKConfig ( RCC_SYSCLK_Div1 );
        RCC_PCLK2Config ( RCC_HCLK_Div1 );
        RCC_PCLK1Config ( RCC_HCLK_Div2 );
        FLASH_SetLatency ( FLASH_Latency_2 );
        FLASH_PrefetchBufferCmd ( FLASH_PrefetchBuffer_Enable );
        RCC_PLLConfig ( RCC_PLLSource_HSE_Div1, RCC_PLLMul_9 );
        RCC_PLLCmd ( ENABLE );
​
        while ( RCC_GetFlagStatus ( RCC_FLAG_PLLRDY ) == RESET );
​
        RCC_SYSCLKConfig ( RCC_SYSCLKSource_PLLCLK );
​
        while ( RCC_GetSYSCLKSource() != 0x08 );
    }
​
    /* 这一句很重要，它决定RTC能不能正常工作(开启相应的时钟) */
    RCC_APB1PeriphClockCmd ( RCC_APB1Periph_PWR | RCC_APB1Periph_BKP, ENABLE );
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_USART1 | RCC_APB2Periph_GPIOA, ENABLE );
}
```

&emsp;&emsp;`RTC_Configuration`函数如下：

``` c
void RTC_Configuration ( void ) {
    /* 使能或者失能RTC和后备寄存器访问(使能)；后备电源要打开，
       PWR_CR的DBP设置为1(允许写入RTC和后备寄存器) */
    PWR_BackupAccessCmd ( ENABLE );
    BKP_DeInit(); /* 复位备份寄存器设置，将外设RCC寄存器重设为缺省值 */
    // RCC_LSEConfig ( RCC_LSE_ON ); /* 设置外部低速晶振(LSE) */
    RCC_LSICmd ( ENABLE ); /* 使能或者失能内部低速晶振(LSI) */
​
    /* 等待LSI晶振就绪 */
    while ( RCC_GetFlagStatus ( RCC_FLAG_LSIRDY ) == RESET );
​
    RCC_RTCCLKConfig ( RCC_RTCCLKSource_LSI ); /* 设置RTC时钟(RTCCLK) */
    RCC_RTCCLKCmd ( ENABLE ); /* 使能或者失能RTC时钟 */
    RTC_WaitForSynchro(); /* 等待RTC寄存器同步完成 */
    RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
    RTC_ITConfig ( RTC_IT_SEC, ENABLE ); /* 使能或者失能指定的RTC中断 */
    RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
    /* 设置RTC预分频的值为32767，则计数频率 = (32.768kHz)/(32767 + 1) = 1Hz */
    RTC_SetPrescaler ( 32767 );
    RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
}
```

&emsp;&emsp;重要代码的解析：

``` c
/* 打开APB1总线上的PWR，BKP时钟 */
RCC_APB1PeriphClockCmd ( RCC_APB1Periph_PWR | RCC_APB1Periph_BKP, ENABLE );
/* 使能RTC和后备寄存器访问 */
PWR_BackupAccessCmd ( ENABLE ); /* PWR_CR的DBP = 1 */
BKP_DeInit(); /* 复位备份寄存器设置 */
/* 打开外部低速晶振器，一定要确定是使用内部的时钟还是外部的时钟(建议开启外部时钟) */
RCC_LSEConfig ( RCC_LSE_ON );
/* 等待LSE晶振就绪 */
while ( RCC_GetFlagStatus ( RCC_FLAG_LSERDY ) == RESET );
RCC_RTCCLKCmd ( ENABLE ); /* 使能或者失能RTC时钟(使能) */
/* 等待RTC寄存器同步(APB1)完成(见原理图，RTCCLK要等待PCLK1信号) */
RTC_WaitForSynchro();
RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
RTC_ITConfig ( RTC_IT_SEC, ENABLE ); /* 使能或者失能指定的RTC中断 */
// RTC_ITConfig ( RTC_IT_ALR, ENABLE ); /* 也可以加上闹钟中断；使能或者失能指定的RTC中断 */
RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
/* 设置RTC预分频的值为32767，则计数频率 = (32.768kHz)/(32767 + 1) = 1Hz */
RTC_SetPrescaler ( 32767 );
RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
/* 设置当前时间值 */
/* 例如2013.02.05的4:29:30，以秒为单位，则t = 30 + 29 * 60 + 14 * 3600 + 5 * 24 * 3600 + ... */
RTC_SetCounter ( ( u32 ) t );
RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
```

&emsp;&emsp;`STM32`的内部`RTC`只提供了一个秒计数器，若需要当前日期，还需进行一番运算，比较麻烦。

``` c
rtc rtc_real;
​
void RTC_Init ( void ) {
    RCC_APB1PeriphClockCmd ( RCC_APB1Periph_PWR | RCC_APB1Periph_BKP, ENABLE );
    PWR_BackupAccessCmd ( ENABLE ); /* 使能RTC和后备寄存器访问 */
​
    /* 读取后备寄存器1的数据 */
    if ( BKP_ReadBackupRegister ( BKP_DR1 ) != 0x5555 ) {
        BKP_DeInit(); /* Reset Backup Domain */
        RCC_LSEConfig ( RCC_LSE_ON ); /* Enable LSE，打开外部低速晶振 */
​
        /* 等待外部低速晶振震荡 需要等待比较长的时间 */
        while ( RCC_GetFlagStatus ( RCC_FLAG_LSERDY ) == RESET );
​
        RCC_RTCCLKConfig ( RCC_RTCCLKSource_LSE ); /* 使用外部晶振32768作为时钟源 */
        RCC_RTCCLKCmd ( ENABLE ); /* 允许RTC */
        RTC_WaitForSynchro(); /* 等待RTC寄存器同步 */
        RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
        RTC_ITConfig ( RTC_IT_SEC, ENABLE ); /* 允许RTC的秒中断(还有闹钟中断和溢出中断可设置) */
        RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
        /* 32768晶振预分频值是32767，不过一般来说晶振都不那么准；如果需要校准晶振，可修改此分频值 */
        RTC_SetPrescaler ( 32776 );
        RTC_WaitForLastTask(); /* 等待最近一次对RTC寄存器的写操作完成 */
        /* 向RTC后备寄存器1写入“0x5555”表示时钟已经配置过了 */
        BKP_WriteBackupRegister ( BKP_DR1, 0x5555 );
        USART1_SendString ( "系统未设置时间" );
    } else { /* 如果RTC已经设置 */
        RTC_WaitForSynchro(); /* 等待RTC与APB同步 */
        RTC_WaitForLastTask();
        RTC_ITConfig ( RTC_IT_SEC, ENABLE ); /* 使能秒中断 */
        RTC_WaitForLastTask();
        USART1_SendString ( "系统已设置时间" );
    }
​
    RCC_ClearFlag(); /* 清除标志 */
}
​
u8 Is_LeapYear ( u16 year ) { /* 该年份是不是闰年：1为是；0为不是 */
    if ( ( year % 4 == 0 ) && ( year % 100 != 0 ) || ( year % 400 == 0 ) ) {
        return 1;
    } else {
        return 0;
    }
}
​
/* 平年的月份日期表 */
const u8 month_table[12] = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
​
/* 把输入的时钟转换为秒钟，以1970年1月1日为基准，1970至2099年为合法年份 */
u8 RTC_Set ( u16 year, u8 month, u8 day, u8 hour, u8 min, u8 sec ) {
    u16 i;
    u32 seccount;
​
    if ( year < 1970 || year > 2099 ) { /* 超限 */
        return 0;
    }
​
    for ( i = 1970; i < year; i++ ) { /* 计算年份的秒数 */
        if ( Is_LeapYear ( i ) ) {
            seccount += 31622400; /* 闰年的秒钟数 */
        } else {
            seccount += 31536000; /* 平年的秒钟数 */
        }
    }
​
    month -= 1;
​
    for ( i = 0; i < month; i++ ) {
        seccount += ( u32 ) month_table[i] * 86400; /* 月份秒钟数相加 */
​
        if ( Is_LeapYear ( year ) && i == 1 ) {
            seccount += 86400; /* 闰年2月份增加一天的秒钟数 */
        }
    }
​
    seccount += ( u32 ) ( day - 1 ) * 86400; /* 把前面日期的秒钟数相加 */
    seccount += ( u32 ) hour * 3600; /* 小时秒钟数 */
    seccount += ( u32 ) min * 60; /* 分钟秒钟数 */
    seccount += sec; /* 最后的秒钟加上去 */
    /* 设置时钟 */
    /* 使能电源时钟，使能备份时钟 */
    RCC_APB1PeriphClockCmd ( RCC_APB1Periph_PWR | RCC_APB1Periph_BKP, ENABLE );
    PWR_BackupAccessCmd ( ENABLE ); /* 使能RTC和后备寄存器访问 */
    RTC_EnterConfigMode(); /* 进入RTC配置模式 */
    RTC_SetCounter ( seccount ); /* 设置RTC计数器的值 */
    RTC_ExitConfigMode(); /* 退出配置模式 */
    RTC_WaitForLastTask(); /* 等待对RTC寄存器的写操作完成 */
    return 1;
}
​
/* 得到当前的时间。返回1表示成功，其他值表示错误 */
u8 RTC_Get ( void ) {
    u32 temp = 0;
    u16 temp1 = 0;
    u32 seccount = 0;
    seccount = RTC_GetCounter(); /* 获取秒计数器的值 */
    temp = seccount / 86400; /* 得到天数(秒钟数对应的) */
​
    if ( temp > 0 ) { /* 超过一天 */
        temp1 = 1970;
​
        while ( temp >= 365 ) {
            if ( Is_LeapYear ( temp1 ) ) { /* 闰年 */
                if ( temp >= 366 ) {
                    temp -= 366;
                } else { /* 当前年份即为此闰年 */
                    break;
                }
            } else {
                temp -= 365;
            }
​
            temp1++;
        }
    }
​
    rtc_real.syear = temp1; /* 得到年份 */
    temp1 = 0;
​
    while ( temp >= 28 ) { /* 超过了一个月 */
        /* 当年是不是闰年(2月份) */
        if ( Is_LeapYear ( rtc_real.syear ) && temp1 == 1 ) {
            if ( temp >= 29 ) {
                temp -= 29; /* 闰年的秒钟数 */
            } else {
                break;
            }
        } else {
            if ( temp >= month_table[temp1] ) {
                temp -= month_table[temp1]; /* 平年 */
            } else {
                break;
            }
        }
​
        temp1++;
    }
​
    rtc_real.smonth = temp1 + 1; /* 得到月份 */
    rtc_real.sday = temp + 1; /* 得到日期 */
    temp = seccount % 86400; /* 得到秒钟数 */
    rtc_real.hour = temp / 3600; /* 小时 */
    rtc_real.min = ( temp % 3600 ) / 60; /* 分钟 */
    rtc_real.sec = ( temp % 3600 ) % 60; /* 秒钟 */
    rtc_real.week = RTC_GetWeek ( rtc_real.syear, rtc_real.smonth, rtc_real.sday );
    return 1;
}
​
const u8 week_table[12] = {0, 3, 3, 6, 1, 4, 6, 2, 5, 0, 3, 5};
​
/* 输入公历日期得到星期(只允许1901至2099年) */
u8 RTC_GetWeek ( u16 year, u8 month, u8 day ) {
    u16 temp;
    u8 yearH, yearL;
    yearH = year / 100;
    yearL = year % 100;
​
    if ( yearH > 19 ) { /* 如果为21世纪，年份数加100 */
        yearL += 100;
    }
​
    /* 所过闰年数只算1900年之后的 */
    temp = yearL + yearL / 4;
    temp = temp % 7;
    temp = temp + day + week_table[month - 1];
​
    if ( yearL % 4 == 0 && month < 3 ) {
        temp--;
    }
​
    return ( temp % 7 );
}
```