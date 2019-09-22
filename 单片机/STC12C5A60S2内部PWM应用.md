---
title: STC12C5A60S2内部PWM应用
date: 2018-12-29 17:24:11
categories: 单片机
---
&emsp;&emsp;代码如下：

``` c
#include "STC12C5A.h"

#define uint unsigned int
#define uchar unsigned char
​
sbit AddPWM = P1 ^ 0; /* 通过P10、P11低电平(按键)加减PWM占空比 */
sbit CutPWM = P1 ^ 1;
​
void DelayMs ( uchar ms );
void init_PWM();
void PWM0_change ( uchar type, uchar change );
void PWM0_set ( uchar empty );
​
void DelayMs ( uchar ms ) { /* 这个延时可能不对，非关键部分，可自己修改 */
    uint i;
​
    while ( ms-- ) {
        for ( i = 0; i < 850; i++ );
    }
}
​
void init_PWM() {
    CCON = 0X00;
    CH = 0;
    CL = 0;
    CMOD = 0X02; /* 0X00：以“系统时钟/12”为时钟源；0X02：“系统时钟/2”；0x08：系统时钟 */
    /* 起始占空比，0XC0：占空比为25%；0X80：占空比为50%；0X40：占空比为75% */
    PWM0_set ( 0X40 );
    // PCA_PWM0 = 0x00; /* 控制占空比的第九位为0 */
    /* 0X42：8位PWM，P1.3输出，无中断；0X53：8位PWM输出，下降沿产生中断；
       0X63：上升沿产生中断；0X73：跳变沿产生中断 */
    CCAPM0 = 0X42;
    CR = 1; /* 计时器开始工作 */
}
​
void PWM0_set ( uchar empty ) { /* 直接设置占空比 */
    CCAP0L = empty;
    CCAP0H = empty;
}
​
/* type为0是减占空比，1是增加占空比；change：0X0C约5%，0X05约2% */
void PWM0_change ( uchar type, uchar change ) {
    if ( type == 0 ) {
        if ( CCAP0L < 0XE6 ) { /* <90% */
            CCAP0L += change;
            CCAP0H += change;
        }
    } else {
        if ( CCAP0L > 0X19 ) { /* >10% */
            CCAP0L -= change;
            CCAP0H -= change;
        }
    }
}
​
void main ( void ) {
    init_PWM(); /* 初始化，PWM输出 */
​
    while ( 1 ) { /* 按键增减PWM占空比 */
        if ( AddPWM == 0 ) {
            DelayMs ( 500 ); /* 按键消抖 */
​
            while ( AddPWM == 0 ); /* 按键释放才跳出循环 */
​
            PWM0_change ( 1, 0X0C );
        }
​
        if ( CutPWM == 0 ) {
            DelayMs ( 500 );
​
            while ( CutPWM == 0 );
​
            PWM0_change ( 0, 0X0C );
        }
    }
}
```