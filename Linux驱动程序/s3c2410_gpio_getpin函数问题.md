---
title: s3c2410_gpio_getpin函数问题
categories: Linux驱动程序
abbrlink: e647a7e5
date: 2019-02-04 09:52:58
---
&emsp;&emsp;在调试`NRF24L01`的`Linux`驱动时，发送程序调试的很顺利，但是接收驱动一直有问题。最后确定`S3C2440`通过`SPI`接口读取`NRF24L01`时的`MISO`存在问题，问题集中在`tmp |= MISO_STU;`。
&emsp;&emsp;`MSIO_STU`定义如下：

``` cpp
#define MISO_STU s3c2410_gpio_getpin(S3C2410_GPG(9)) /* 读取GPIO状态 */
```

`s3c2410_gpio_getpin`函数原型如下：

``` cpp
unsigned int s3c2410_gpio_getpin ( unsigned int pin ) {
    void __iomem *base = S3C24XX_GPIO_BASE ( pin );
    unsigned long offs = S3C2410_GPIO_OFFSET ( pin );
    return __raw_readl ( base + 0x04 ) & ( 1 << offs );
}
```

`s3c2410_gpio_getpin`的返回值是`GPxDAT`寄存器的值与所要读取的`GPIO`对应的`bit mask`相与以后的值。`0`表示该`GPIO`对应的`bit`为`0`，非`0`表示该`bit`为`1`。所以对于`s3c2410_gpio_getpin(S3C2410_GPG(9))`，如果`GPG9`为低电平则返回的是`0`，如果是高电平则返回的是`GPxDAT`中的`GPG9`对应位的值为`0x0100`而不是`0x0001`。修改`tmp |= MISO_STU;`如下：

``` cpp
if ( MISO_STU ) {
    tmp |= 0x01;
}
```

修改后的`SPI`读写程序如下：

``` cpp
uint8 SPI_RW ( uint8 tmp ) {
    uint8 bit_ctr;
​
    for ( bit_ctr = 0 ; bit_ctr < 8 ; bit_ctr++ ) { /* output 8-bit */
        if ( tmp & 0x80 ) { /* output 'tmp', MSB to MOSI */
            MOSI_H;
        } else {
            MOSI_L;
        }
​
        tmp <<= 1; /* shift next bit into MSB */
        SCK_H; /* Set SCK high */
        ndelay ( 60 );
​
        if ( MISO_STU ) { /* capture current MISO bit */
            tmp |= 0x01 ;
        }
​
        SCK_L; /* then set SCK low again */
        ndelay ( 60 );
    }
​
    return ( tmp ); /* return read tmp */
}
```