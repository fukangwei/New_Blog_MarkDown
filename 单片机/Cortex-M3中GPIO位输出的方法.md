---
title: Cortex-M3中GPIO位输出的方法
categories: 单片机
abbrlink: faa9c4bc
date: 2019-01-19 14:53:57
---
### 端口位设置/清除

&emsp;&emsp;在`STM32F1xx`系列芯片中，可对`BSRR`、`BRR`寄存器相应的位置`1`，以实现置位和清零操作：

``` c
GPIOA->BSRR = ( 1 << 3 ); /* 设置端口A的位3为1 */
GPIOA->BRR  = ( 1 << 3 ); /* 清除端口A的位3为0 */
```

&emsp;&emsp;在`LPC17xx`系列芯片中，可对`FIOSET`、`FIOCLR`寄存器相应的位置`1`，以实现置位和清零操作：

``` c
LPC_GPIO2->FIOSET = ( 1 << 3 ); /* 设置端口2的位3为1 */
LPC_GPIO2->FIOCLR = ( 1 << 3 ); /* 清除端口2的位3为0 */
```

### 端口直接输出

&emsp;&emsp;在`STM32F1xx`系列芯片中，可对`ODR`寄存器相应的位置`1`或`0`，以实现置位和清零操作：

``` c
GPIOA->ODR |= ( 1 << 3 ); /* 端口A的位3输出1 */
GPIOA->ODR &= ~ ( 1 << 3 ); /* 端口A的位3输出0 */
```

&emsp;&emsp;在`LPC17xx`系列芯片中，可对`FIOPIN`寄存器相应的位置`1`或`0`，以实现置位和清零操作：

``` c
LPC_GPIO2->FIOPIN |= ( 1 << 3 ); /* 端口2的位3输出1 */
LPC_GPIO2->FIOPIN &= ~ ( 1 << 3 ); /* 端口2的位3输出0 */
```

### 端口位带输出

&emsp;&emsp;参考`Cortex-M3权威指南`第五章第`5`小节的位带操作(`87`至`92`页)。为了简化位带操作，可以定义一些宏。比如可以建立一个把`位带地址 + 位序号`转换成别名地址的宏，再建立一个把别名地址转换成指针类型的宏。

``` c
/* 把“位带地址 + 位序号”转换成别名地址的宏 */
#define BITBAND(addr, bitnum) ((addr & 0xF0000000) + 0x2000000 + \
                              ((addr & 0xFFFFF) << 5) + (bitnum << 2))
/* 把该地址转换成一个指针 */
#define MEM_ADDR(addr) *((volatile unsigned long *) (addr))
/* 使用位带别名地址访问 */
#define BIT_ADDR(addr, bitnum) MEM_ADDR(BITBAND(addr, bitnum))
```

&emsp;&emsp;`STM32F1xx`系列芯片的应用：

``` c
#define PAout(n) BIT_ADDR((uint32_t) & GPIOA->ODR, n)
PAout ( 3 ) = 1; /* 端口A的位3输出1 */
PAout ( 3 ) = 0; /* 端口A的位3输出0 */
```

&emsp;&emsp;`LPC17xx`系列芯片：

``` c
#define P2out(n) BIT_ADDR((uint32_t) & LPC_GPIO2->FIOPIN, n)
P2out ( 3 ) = 1; /* 端口2的位3输出1 */
P2out ( 3 ) = 0; /* 端口2的位3输出0 */
```

### 端口位域输出

&emsp;&emsp;定义一个端口位域，端口为`16`位的就定义`16`位(`STM32F1xx`)，端口为`32`位的就定义`32`位(`LPC17xx`)：

``` c
#pragma anon_unions
​
typedef union {
    uint32_t WORDS;
    struct {
        int bit00: 1;
        int bit01: 1;
        int bit02: 1;
        int bit03: 1;
        int bit04: 1;
        int bit05: 1;
        int bit06: 1;
        int bit07: 1;
        int bit08: 1;
        int bit09: 1;
        int bit10: 1;
        int bit11: 1;
        int bit12: 1;
        int bit13: 1;
        int bit14: 1;
        int bit15: 1;
        int bit16: 1;
        int bit17: 1;
        int bit18: 1;
        int bit19: 1;
        int bit20: 1;
        int bit21: 1;
        int bit22: 1;
        int bit23: 1;
        int bit24: 1;
        int bit25: 1;
        int bit26: 1;
        int bit27: 1;
        int bit28: 1;
        int bit29: 1;
        int bit30: 1;
        int bit31: 1;
    };
} PORT;
```

&emsp;&emsp;`STM32F1xx`系列芯片的应用：

``` c
#define PAout03 (((PORT *) (& GPIOA->ODR))->bit03)
PAout03 = 1; /* 端口A的位3输出1 */
PAout03 = 0; /* 端口A的位3输出0 */
```

&emsp;&emsp;`LPC17xx`系列芯片的应用：

``` c
#define P2out03 (((PORT *) (& LPC_GPIO2->FIOPIN))->bit03)
P2out03 = 1; /* 端口2的位3输出1 */
P2out03 = 0; /* 端口2的位3输出0 */
```

### 综述

&emsp;&emsp;对于以上`4`种方法，`1`、`2`两种较为多见；方法`3`为位带操作，速度最快，但只对具备位带的`MCU`有效；方法`4`是一种新颖的通用方法，只要找到输入或输出寄存器即可，对任意`MCU`有效。