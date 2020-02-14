---
title: 51单片机printf函数
categories: 单片机
abbrlink: ff571887
date: 2018-12-29 17:21:55
---
&emsp;&emsp;代码如下：<!--more-->

``` cpp
#include <reg52.h>
#include <stdio.h> /* “stdio.h”用于printf函数原型 */
​
void delay ( unsigned int z );
void uart_init ( void ); /* 串行口初始化 */
​
int main ( void ) {
    int a = 99;
    char *string = "abde";
    uart_init();
​
    while ( 1 ) {
        printf ( "%d  %x  %c  %s  %p\n", a, a, ( char ) a, string, string );
        delay ( 1000 );
    }
​
    return 0;
}
​
void uart_init ( void ) {
    TMOD = 0x20; /* 即0010_0000、定时器/计数器1、工作方式2 */
    TH1 = 0xfd; /* 设置波特率为9600 */
    TL1 = 0xfd;
    TR1 = 1; /* 启动定时器/计数器1 */
    SCON = 0x50; /* 0101_0000、串口工作方式1、允许串行控制 */
    PCON = 0x00; /* 设置SMOD = 0 */
    IE = 0x90; /* CPU允许中断，串行允许中断 */
    TI = 1; /* 直接使用printf必须加入此句才能实现发送 */
}
​
void delay ( unsigned int z ) {
    unsigned int x, y;
​
    for ( x = z; x > 0; x-- )
        for ( y = 110; y > 0; y-- );
}
```