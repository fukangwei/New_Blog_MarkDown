---
title: Contiki的SPI接口
categories: Contiki和uip
abbrlink: ad1986fb
date: 2019-02-04 23:50:56
---
### SPI宏定义

&emsp;&emsp;`Contiki`源码的`SPI`宏定义如下：<!--more-->

``` cpp
#ifndef __SPI_H__
#define __SPI_H__
​
#ifdef SPI_WAITFORTxREADY
    #define SPI_WAITFORTx_BEFORE() SPI_WAITFORTxREADY()
    #define SPI_WAITFORTx_AFTER()
    #define SPI_WAITFORTx_ENDED() SPI_WAITFOREOTx()
#else /* SPI_WAITFORTxREADY */
    #define SPI_WAITFORTx_BEFORE()
    #define SPI_WAITFORTx_AFTER() SPI_WAITFOREOTx()
    #define SPI_WAITFORTx_ENDED()
#endif /* SPI_WAITFORTxREADY */
​
extern unsigned char spi_busy;
​
void spi_init ( void );
​
/* Write one character to SPI */
#define SPI_WRITE(data)         \
    do {                        \
        SPI_WAITFORTx_BEFORE(); \
        SPI_TXBUF = data;       \
        SPI_WAITFOREOTx();      \
    } while(0)
​
/* Write one character to SPI - will not wait for end
   useful for multiple writes with wait after final */
#define SPI_WRITE_FAST(data)    \
    do {                        \
        SPI_WAITFORTx_BEFORE(); \
        SPI_TXBUF = data;       \
        SPI_WAITFORTx_AFTER();  \
    } while(0)
​
/* Read one character from SPI */
#define SPI_READ(data)     \
    do {                   \
        SPI_TXBUF = 0;     \
        SPI_WAITFOREORx(); \
        data = SPI_RXBUF;  \
    } while(0)
​
/* Flush the SPI read register */
#define SPI_FLUSH() \
    do {            \
        SPI_RXBUF;  \
    } while(0);
​
#endif /* __SPI_H__ */
```

### platform-conf.h中的SPI宏定义

&emsp;&emsp;`Contiki`中有两个重要的头文件，一个是`contiki-conf.h`，另一个是`platform-conf.h`。该头文件中存在所有和平台有关的宏定义，其中包括`SPI`接口。
&emsp;&emsp;`MSP430`平台(`exp5438`)如下：

``` cpp
#define SPI_TXBUF UCB0TXBUF
#define SPI_RXBUF UCB0RXBUF
/* USART0 Tx ready? */
/* 发送或接收正在进行，UCBUSY置位 */
#define SPI_WAITFOREOTx()  while ((UCB0STAT & UCBUSY) != 0)
/* USART0 Rx ready? */
/* 当RXBUF接收到一个字符，UCRXIFG置位 */
#define SPI_WAITFOREORx()  while ((UCB0IFG & UCRXIFG) == 0)
/* USART0 Tx buffer ready? */
/* 当TXBUF为空时，UCTXIFG置位 */
#define SPI_WAITFORTxREADY()  while ((UCB0IFG & UCTXIFG) == 0)
```

&emsp;&emsp;`AVR`平台(`micaz`)如下：

``` cpp
#define SPI_TXBUF SPDR
#define SPI_RXBUF SPDR
#define SPI_WAITFOREOTx() do { while (!(SPSR & BV(SPIF))); } while (0)
#define SPI_WAITFOREORx() do { while (!(SPSR & BV(SPIF))); } while (0)
```

- `SPI_WAITFOREOTx`为等待发送完成，`MSP430F5438`检测忙碌标志，`Atmeg128`检测发送接收完成标志位。
- `SPI_WAITFOREORx`为等待接收完成，`MSP430F5438`检测接收缓冲区标志位，`Atmeg128`检测发送接收完成标志。

### SPI宏定义展开

&emsp;&emsp;`EXP5438`的`SPI`宏展开如下：

``` cpp
/* Write one character to SPI */
#define SPI_WRITE(data)         \
    do {                        \
        SPI_WAITFORTx_BEFORE(); \
        SPI_TXBUF = data;       \
        SPI_WAITFOREOTx();      \
    } while(0)
​
#define SPI_WRITE(data)                \
    do {                               \
        UCB0TXBUF = data;              \
        while((UCB0STAT&UCBUSY) != 0); \
    } while(0)
​
/* Write one character to SPI - will not wait for end
   useful for multiple writes with wait after final */
#define SPI_WRITE_FAST(data)    \
    do {                        \
        SPI_WAITFORTx_BEFORE(); \
        SPI_TXBUF = data;       \
        SPI_WAITFORTx_AFTER();  \
    } while(0)
​
#define SPI_WRITE_FAST(data)           \
    do {                               \
        UCB0TXBUF = data;              \
        while((UCB0STAT&UCBUSY) != 0); \
    } while(0)
​
/* Read one character from SPI */
#define SPI_READ(data)     \
    do {                   \
        SPI_TXBUF = 0;     \
        SPI_WAITFOREORx(); \
        data = SPI_RXBUF;  \
    } while(0)
​
#define SPI_READ(data)                 \
    do {                               \
        UCB0RXBUF = 0;                 \
        while((UCB0IFG&UCRXIFG) == 0); \
        data = SPI_RXBUF;              \
    } while(0)
```

### STM32平台

&emsp;&emsp;如果`STM32`连接`CC2520`，那么`SPI`的关键定义如下(暂未使用硬件平台进行测试，并假设使用`SPI1`)：

``` cpp
#define SPI_TXBUF SPI1->DR
#define SPI_RXBUF SPI1->DR
/* 等待发送完成 */
#define SPI_WAITFOREOTx() while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_TXE) == RESET);
/* 等待接收完成 */
#define SPI_WAITFOREORx() while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_RXNE) == RESET);
/* 同为等待发送完成 */
#define SPI_WAITFORTxREADY() while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_TXE) == RESET);
```