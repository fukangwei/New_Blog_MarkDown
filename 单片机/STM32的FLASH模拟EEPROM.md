---
title: STM32的FLASH模拟EEPROM
date: 2019-01-19 17:11:01
categories: 单片机
---
&emsp;&emsp;内置闪存模块可以在通用地址空间直接寻址，任何`32`位数据的读操作都能访问闪存模块的内容并得到相应的数据。读接口在闪存端包含一个读控制器，还包含一个`AHB`接口与`CPU`衔接。这个接口的主要工作是产生读闪存的控制信号并预取`CPU`要求的指令块，预取指令块仅用于在`I-Code`总线上的取指操作，数据常量是通过`D-Code`总线访问的。这两条总线的访问目标是相同的闪存模块，访问`D-Code`将比预取指令优先级高。
&emsp;&emsp;这里要特别注意闪存等待时间，因为`CPU`运行速度比`FLASH`快得多，`STM32F103`的`FLASH`最快访问速度为`24Mhz`。如果`CPU`频率超过这个速度，那么必须加入等待时间。例如一般使用`72MHz`的主频，那么`FLASH`等待周期就必须设置为`2`，该设置使用`FLASH_ACR`寄存器。
&emsp;&emsp;使用`STM32`的官方固件库提供函数的操作`FLASH`，这些函数位于`stm32f10x_flash.c`以及`stm32f10x_flash.h`。

### 锁定解锁函数

&emsp;&emsp;在对`FLASH`进行写操作前必须先解锁，解锁操作也就是向`FLASH_KEYR`寄存器写入特定的序列(`KEY1`和`KEY2`)。固件库函数如下：

``` c
void FLASH_Unlock ( void );
```

同样的道理，在对`FLASH`写操作完成之后，我们要锁定`FLASH`，使用的库函数如下：

``` c
void FLASH_Lock ( void );
```

### 写操作函数

&emsp;&emsp;固件库提供了三个`FLASH`写函数：

``` c
FLASH_Status FLASH_ProgramWord ( uint32_t Address, uint32_t Data );
FLASH_Status FLASH_ProgramHalfWord ( uint32_t Address, uint16_t Data );
FLASH_Status FLASH_ProgramOptionByteData ( uint32_t Address, uint8_t Data );
```

`FLASH_ProgramWord`为`32`位字写入函数，其他分别为`16`位半字写入和用户选择字节写入函数。这里需要说明，`32`位字节写入实际上是写入的两次16位数据，写完第一次后地址`+ 2`，这与`STM32`闪存的编程每次必须写入`16`位并不矛盾。

### 擦除函数

&emsp;&emsp;固件库提供三个`FLASH`擦除函数：

``` c
FLASH_Status FLASH_ErasePage ( uint32_t Page_Address );
FLASH_Status FLASH_EraseAllPages ( void );
FLASH_Status FLASH_EraseOptionBytes ( void );
```

### 获取FLASH状态

&emsp;&emsp;主要用到的函数如下：

``` c
FLASH_Status FLASH_GetStatus ( void );
```

返回值是通过枚举类型定义的：

``` c
typedef enum {
    FLASH_BUSY = 1,  /* 忙        */
    FLASH_ERROR_PG,  /* 编程错误   */
    FLASH_ERROR_WRP, /* 写保护错误 */
    FLASH_COMPLETE,  /* 操作完成   */
    FLASH_TIMEOUT    /* 操作超时   */
} FLASH_Status;
```

从这里可以看到`FLASH`操作的`5`个状态。

### 等待操作完成函数

&emsp;&emsp;在执行闪存写操作时，任何对闪存的读操作都会锁住总线，在写操作完成后读操作才能正确地进行，即在进行写或擦除操作时，不能进行代码或数据的读取操作。所以在每次操作之前，都要等待上一次操作完成。使用的函数为：

``` c
FLASH_Status FLASH_WaitForLastOperation ( uint32_t Timeout );
```

参数为等待时间，返回值是`FLASH`的状态。

### 读FLASH特定地址数据函数

&emsp;&emsp;有写就必定有读，而读取`FLASH`指定地址的半字的函数固件库并没有给出来，这里我们自己实现了一个函数：

``` c
u16 STMFLASH_ReadHalfWord ( u32 faddr ) {
    return * ( vu16 * ) faddr;
}
```

&emsp;&emsp;`stmflash.h`如下：

``` c
#ifndef __STMFLASH_H__
#define __STMFLASH_H__
​
#include "sys.h"
​
/* 用户根据自己的需要设置 */
#define STM32_FLASH_SIZE 512 /* 所选STM32的FLASH容量大小(单位为K) */
#define STM32_FLASH_WREN 1 /* 使能FLASH写入(0：不使能；1：使能) */
#define STM32_FLASH_BASE 0x08000000 /* STM32的FLASH起始地址 */
​
u16 STMFLASH_ReadHalfWord ( u32 faddr ); /* 读出半字 */
/* 指定地址开始写入指定长度的数据 */
void STMFLASH_WriteLenByte ( u32 WriteAddr, u32 DataToWrite, u16 Len );
/* 指定地址开始读取指定长度数据 */
u32 STMFLASH_ReadLenByte ( u32 ReadAddr, u16 Len );
/* 从指定地址开始写入指定长度的数据 */
void STMFLASH_Write ( u32 WriteAddr, u16 *pBuffer, u16 NumToWrite );
/* 从指定地址开始读出指定长度的数据 */
void STMFLASH_Read ( u32 ReadAddr, u16 *pBuffer, u16 NumToRead );
void Test_Write ( u32 WriteAddr, u16 WriteData ); /* 测试写入 */
​
#endif
```

&emsp;&emsp;`stmflash.c`如下：

``` c
#include "stmflash.h"
#include "delay.h"
#include "usart.h"
​
/* 功能：读取指定地址的半字(16位数据)
   faddr：读地址(此地址必须为2的倍数)
   返回值：对应数据 */
u16 STMFLASH_ReadHalfWord ( u32 faddr ) {
    return * ( vu16 * ) faddr;
}
​
#if STM32_FLASH_WREN /* 如果使能了写 */
/* 功能：不检查的写入
   WriteAddr：起始地址
   pBuffer：数据指针
   NumToWrite：半字(16位)数 */
void STMFLASH_Write_NoCheck ( u32 WriteAddr, u16 *pBuffer, u16 NumToWrite ) {
    u16 i;
​
    for ( i = 0; i < NumToWrite; i++ ) {
        FLASH_ProgramHalfWord ( WriteAddr, pBuffer[i] );
        WriteAddr += 2; /* 地址增加2 */
    }
}
​
/* 功能：从指定地址开始写入指定长度的数据
   WriteAddr：起始地址(此地址必须为2的倍数)
   pBuffer：数据指针
   NumToWrite：半字(16位)数(就是要写入的16位数据的个数) */
#if STM32_FLASH_SIZE < 256
    #define STM_SECTOR_SIZE 1024 /* 字节 */
#else
    #define STM_SECTOR_SIZE 2048
#endif
​
u16 STMFLASH_BUF[STM_SECTOR_SIZE / 2]; /* 最多是2K字节 */
​
void STMFLASH_Write ( u32 WriteAddr, u16 *pBuffer, u16 NumToWrite ) {
    u32 secpos;    /* 扇区地址 */
    u16 secoff;    /* 扇区内偏移地址(16位字计算) */
    u16 secremain; /* 扇区内剩余地址(16位字计算) */
    u16 i;
    u32 offaddr;   /* 去掉0X08000000后的地址 */
​
    /* 非法地址 */
    if ( WriteAddr < STM32_FLASH_BASE || ( WriteAddr >= ( STM32_FLASH_BASE + 1024 * STM32_FLASH_SIZE ) ) ) {
        return;
    }
​
    FLASH_Unlock(); /* 解锁 */
    offaddr = WriteAddr - STM32_FLASH_BASE; /* 实际偏移地址 */
    secpos = offaddr / STM_SECTOR_SIZE; /* 扇区地址，0至127 for STM32F103RBT6 */
    secoff = ( offaddr % STM_SECTOR_SIZE ) / 2; /* 在扇区内的偏移(2个字节为基本单位) */
    secremain = STM_SECTOR_SIZE / 2 - secoff; /* 扇区剩余空间大小 */
​
    if ( NumToWrite <= secremain ) { /* 不大于该扇区范围 */
        secremain = NumToWrite;
    }
​
    while ( 1 ) {
        /* 读出整个扇区的内容 */
        STMFLASH_Read ( secpos * STM_SECTOR_SIZE + STM32_FLASH_BASE, \
                        STMFLASH_BUF, STM_SECTOR_SIZE / 2 );
​
        for ( i = 0; i < secremain; i++ ) { /* 校验数据 */
            if ( STMFLASH_BUF[secoff + i] != 0XFFFF ) { /* 检验需要擦除 */
                break;
            }
        }
​
        if ( i < secremain ) { /* 如果需要擦除 */
            /* 擦除这个扇区 */
            FLASH_ErasePage ( secpos * STM_SECTOR_SIZE + STM32_FLASH_BASE );
​
            for ( i = 0; i < secremain; i++ ) { /* 复制 */
                STMFLASH_BUF[i + secoff] = pBuffer[i];
            }
​
            /* 写入整个扇区 */
            STMFLASH_Write_NoCheck ( secpos * STM_SECTOR_SIZE + STM32_FLASH_BASE, \
                                     STMFLASH_BUF, STM_SECTOR_SIZE / 2 );
        } else {
            STMFLASH_Write_NoCheck ( WriteAddr, pBuffer, secremain );
        }
​
        if ( NumToWrite == secremain ) {
            break; /* 写入结束了 */
        } else { /* 写入未结束 */
            secpos++; /* 扇区地址增1 */
            secoff = 0; /* 偏移位置为0 */
            pBuffer += secremain; /* 指针偏移 */
            WriteAddr += secremain; /* 写地址偏移 */
            NumToWrite -= secremain; /* 字节(16位)数递减 */
​
            if ( NumToWrite > ( STM_SECTOR_SIZE / 2 ) ) {
                secremain = STM_SECTOR_SIZE / 2; /* 下一个扇区还是写不完 */
            } else {
                secremain = NumToWrite; /* 下一个扇区可以写完了 */
            }
        }
    };
​
    FLASH_Lock(); /* 上锁 */
}
#endif
​
/* 功能：从指定地址开始读出指定长度的数据
   ReadAddr：始地址
   pBuffer：数据指针
   NumToWrite：半字(16位)数 */
void STMFLASH_Read ( u32 ReadAddr, u16 *pBuffer, u16 NumToRead ) {
    u16 i;
​
    for ( i = 0; i < NumToRead; i++ ) {
        pBuffer[i] = STMFLASH_ReadHalfWord ( ReadAddr ); /* 读取2个字节 */
        ReadAddr += 2; /* 偏移2个字节 */
    }
}
​
/* WriteAddr：起始地址；WriteData：要写入的数据 */
void Test_Write ( u32 WriteAddr, u16 WriteData ) {
    STMFLASH_Write ( WriteAddr, &WriteData, 1 ); /* 写入一个字 */
}
```

&emsp;&emsp;`main.c`如下：

``` c
#include "led.h"
#include "delay.h"
#include "sys.h"
#include "usart.h"
#include "stmflash.h"
#include "string.h"
​
const u8 TEXT_Buffer[] = {"STM32 FLASH TEST"}; /* 要写入到STM32中FLASH的字符串数组 */
#define SIZE sizeof(TEXT_Buffer) /* 数组长度 */
/* 设置FLASH的保存地址(必须为偶数，且其值要大于本代码所占用FLASH的大小 + 0X08000000) */
#define FLASH_SAVE_ADDR 0X08020000
​
int main ( void ) {
    u8 datatemp[SIZE];
    SystemInit();
    delay_init ( 72 );
    NVIC_Configuration();
    uart_init ( 9600 );
    LED_Init();
    STMFLASH_Write ( FLASH_SAVE_ADDR, ( u16 * ) TEXT_Buffer, SIZE );
    printf ( "I write %s\r\n",  TEXT_Buffer ) ;
    delay_ms ( 500 );
​
    while ( 1 ) {
        memset ( datatemp, 0, sizeof ( datatemp ) );
        STMFLASH_Read ( FLASH_SAVE_ADDR, ( u16 * ) datatemp, SIZE );
        printf ( "I read %s\r\n",  datatemp ) ;
        LED = !LED;
        delay_ms ( 500 );
    }
}
```