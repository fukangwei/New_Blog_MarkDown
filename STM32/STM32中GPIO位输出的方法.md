---
title: STM32中GPIO位输出的方法
categories: 单片机
date: 2019-01-19 14:53:57
---
### 端口位设置与清零

&emsp;&emsp;在`STM32`中，将寄存器`BSRR`、`BRR`相应的位设为`1`，就可以实现置位和清零：<!--more-->

``` cpp
GPIOA->BSRR = ( 1 << 3 ); /* 设置端口A的位3为1 */
GPIOA->BRR  = ( 1 << 3 ); /* 清除端口A的位3为0 */
```

### 端口直接输出

&emsp;&emsp;在`STM32`中，将寄存器`ODR`相应的位设为`1`或`0`，就可以实现输出`1`或`0`：

``` cpp
GPIOA->ODR |=   ( 1 << 3 ); /* 端口A的位3输出1 */
GPIOA->ODR &= ~ ( 1 << 3 ); /* 端口A的位3输出0 */
```

### 端口位带输出

&emsp;&emsp;`STM32`的位带实现如下：

``` cpp
#define BITBAND(addr, bitnum) ((addr & 0xF0000000) + 0x2000000 + ((addr & 0xFFFFF) << 5) + (bitnum << 2))
#define MEM_ADDR(addr) *((volatile unsigned long *) (addr)) /* 把该地址转换成一个指针 */
#define BIT_ADDR(addr, bitnum) MEM_ADDR(BITBAND(addr, bitnum)) /* 使用位带别名地址访问 */
```

&emsp;&emsp;实际应用如下：

``` cpp
#define PAout(n) BIT_ADDR((uint32_t) & GPIOA->ODR, n)
PAout ( 3 ) = 1; /* 端口A的位3输出1 */
PAout ( 3 ) = 0; /* 端口A的位3输出0 */
```

### 端口位域输出

&emsp;&emsp;定义一个端口位域：

``` cpp
#pragma anon_unions

typedef union {
    uint32_t WORDS;
    struct {
        int bit00: 1; int bit01: 1; int bit02: 1; int bit03: 1;
        int bit04: 1; int bit05: 1; int bit06: 1; int bit07: 1;
        int bit08: 1; int bit09: 1; int bit10: 1; int bit11: 1;
        int bit12: 1; int bit13: 1; int bit14: 1; int bit15: 1;
    };
} PORT;
```

&emsp;&emsp;实际应用如下：

``` cpp
#define PAout03 (((PORT *) (& GPIOA->ODR))->bit03)
PAout03 = 1; /* 端口A的位3输出1 */
PAout03 = 0; /* 端口A的位3输出0 */
```