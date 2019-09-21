---
title: LPC11XX驱动LCD1602程序
date: 2018-12-29 18:31:29
categories: 单片机
---
&emsp;&emsp;`LCD.c`如下(单片机时钟频率为`12MHz`)：

``` c
#include "LPC11XX.h"
#include "gpio.h"
#include "main.h"
#include "Delay.h"
#include "LCD.h"
​
void Set_GPIO_Output ( void ) { /* 设置D0至D7为输出口 */
    LPC_GPIO2->DIR |= ( 0x1 << 0 );
    LPC_GPIO2->DIR |= ( 0x1 << 1 );
    LPC_GPIO2->DIR |= ( 0x1 << 2 );
    LPC_GPIO2->DIR |= ( 0x1 << 3 );
    LPC_GPIO2->DIR |= ( 0x1 << 4 );
    LPC_GPIO2->DIR |= ( 0x1 << 5 );
    LPC_GPIO2->DIR |= ( 0x1 << 6 );
    LPC_GPIO2->DIR |= ( 0x1 << 7 );
}
​
void Set_GPIO_Input ( void ) { /* 设置D0至D7为输入口 */
    LPC_GPIO2->DIR &= ~ ( 0x1 << 0 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 1 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 2 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 3 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 4 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 5 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 6 );
    LPC_GPIO2->DIR &= ~ ( 0x1 << 7 );
}
​
void LCD_GPIO_Init ( void ) {
    /* 设置P2.0至P2.10为GPIO */
    LPC_IOCON->PIO2_0 &= ( ~0x07 );
    LPC_IOCON->PIO2_1 &= ( ~0x07 );
    LPC_IOCON->PIO2_2 &= ( ~0x07 );
    LPC_IOCON->PIO2_3 &= ( ~0x07 );
    LPC_IOCON->PIO2_4 &= ( ~0x07 );
    LPC_IOCON->PIO2_5 &= ( ~0x07 );
    LPC_IOCON->PIO2_6 &= ( ~0x07 );
    LPC_IOCON->PIO2_7 &= ( ~0x07 );
    LPC_IOCON->PIO2_8 &= ( ~0x07 );
    LPC_IOCON->PIO2_9 &= ( ~0x07 );
    LPC_IOCON->PIO2_10 &= ( ~0x07 );
    /* 设置D0至D7为输出 */
    LPC_GPIO2->DIR |= ( 0x1 << 0 );
    LPC_GPIO2->DIR |= ( 0x1 << 1 );
    LPC_GPIO2->DIR |= ( 0x1 << 2 );
    LPC_GPIO2->DIR |= ( 0x1 << 3 );
    LPC_GPIO2->DIR |= ( 0x1 << 4 );
    LPC_GPIO2->DIR |= ( 0x1 << 5 );
    LPC_GPIO2->DIR |= ( 0x1 << 6 );
    LPC_GPIO2->DIR |= ( 0x1 << 7 );
    LPC_GPIO2->DIR |= ( 0x1 << 8 ); /* RS，设为输出 */
    LPC_GPIO2->DIR |= ( 0x1 << 9 ); /* R/W，设为输出 */
    LPC_GPIO2->DIR |= ( 0x1 << 10 ); /* EN，设为输出 */
    LPC_GPIO3->DIR |= ( 0x1 << 3 ); /* BackLighting，液晶屏背光设为输出 */
    GPIOSetValue ( 3, 3, 1 ); /* BackLighting位写1，开启液晶屏背光，写0关闭液晶屏背光 */
}
​
void Write_Data_Port ( unsigned char Data ) { /* 写数据到D0至D7 */
    LPC_GPIO2->DATA &= ~ ( 0xff );
    LPC_GPIO2->DATA |= Data;
}
​
unsigned char Read_Data_Port ( void ) { /* 读D0至D7的数据 */
    return ( LPC_GPIO2->DATA );
}
​
unsigned char Chk_LCD_busy ( void ) { /* 读液晶忙通道数据 */
    unsigned char gR_data;
    unsigned char gwait_time = 0xff; /* 设置忙超时数 */
    Set_GPIO_Input(); /* 接收口设为输入口 */
    LCD_RS_Low(); /* 表示状态 */
    LCD_RW_High(); /* 选择读 */
    LCD_EN_High();
    Delay_Us ( 30 );
    gR_data = Read_Data_Port();
    Delay_Us ( 10 );
​
    while ( TESTBIT ( gR_data, 7 ) ) { /* 表示busy */
        --gwait_time;
​
        if ( !gwait_time ) {
            LCD_EN_Low();
            Set_GPIO_Output(); /* 恢复为输出口 */
            return 0;
        }
    }
​
    LCD_EN_Low();
    Set_GPIO_Output(); /* 恢复为输出口 */
    return 1;
}
​
/* 参数gcmd是指令，gvalue为是否查忙 */
void Write_LCD_command ( unsigned char gcmd, unsigned char gvalue ) {
    if ( gvalue ) { /* 写命令时大部分情况下是在LCD空闲模式下写 */
        if ( Chk_LCD_busy() ) {
            LCD_RS_Low(); /* 选择指令 */
            LCD_RW_Low(); /* 选择写 */
            LCD_EN_High(); /* 使能 */
            Write_Data_Port ( gcmd ); /* 赋指令 */
            Delay_Us ( 15 );
            LCD_EN_Low();
        }
    } else {
        LCD_RS_Low(); /* 选择指令 */
        LCD_RW_Low(); /* 选择写 */
        LCD_EN_High(); /* 使能 */
        Write_Data_Port ( gcmd ); /* 赋指令 */
        Delay_Us ( 10 );
        LCD_EN_Low();
    }
}
​
void Write_LCD_data ( unsigned char gdata ) {
    if ( Chk_LCD_busy() ) { /* 写数据必须是在LCD空闲模式下才能写 */
        LCD_RS_High(); /* 选择数据 */
        LCD_RW_Low(); /* 选择写 */
        LCD_EN_High(); /* 使能 */
        Write_Data_Port ( gdata );
        Delay_Us ( 15 );
        LCD_EN_Low();
    }
}
​
void INIT_LCD ( void ) {
    LCD_GPIO_Init();
    Delay_Ms ( 15 );
    /* 设置“16*2”显示，“5*7”点阵，8位数据传送，不检测忙信号 */
    Write_LCD_command ( 0x38, 0 );
    Delay_Ms ( 5 );
    Write_LCD_command ( 0x38, 0 );
    Delay_Ms ( 5 );
    Write_LCD_command ( 0x38, 0 );
    Delay_Us ( 100 );
    /* 设置“16*2”显示、“5*7”点阵、8位数据传送、检测忙信号 */
    Write_LCD_command ( 0x38, 1 );
    Write_LCD_command ( 0x08, 1 ); /* 关闭显示、无光标、检测忙信号 */
    Write_LCD_command ( 0x01, 1 ); /* 清屏、光标归位、“AC = 0”、检测忙信号 */
    Delay_Ms ( 2 );
    Write_LCD_command ( 0x06, 1 ); /* 显示光标右移位置、检测忙信号 */
    Write_LCD_command ( 0x0C, 1 ); /* 显示功能开、无光标、检测忙信号 */
}
​
/* 参数gadd_start是列号，gline是行号，glength是数据长度，pdata是数组元素 */
void Display_LCD_string (
    unsigned char gadd_start, unsigned char gline,
    unsigned char glength, const unsigned char *pdata ) {
    unsigned char gaddress;
    unsigned char gcount = 0;
​
    if ( !gline ) { /* 第0行 */
        gaddress = 0x80 + gadd_start; /* 地址对应 */
    } else {
        gaddress = 0xc0 + gadd_start; /* 第一行 */
    }
​
    for ( ; gcount < glength; gcount++ ) {
        Write_LCD_command ( gaddress, 1 ); /* 设定数据地址 */
        Write_LCD_data ( *pdata ); /* 取设定地址里的数据 */
        gaddress++;
        pdata++;
    }
}
​
/* 参数x是起始地址横坐标，y是起始地址纵坐标，gdata是要显示的字符 */
void DispChar_XY_LCD ( unsigned char x, unsigned char y, unsigned char gdata ) {
    unsigned char gaddress;
​
    if ( !y ) {
        gaddress = 0x80 + x;
    } else {
        gaddress = 0xc0 + x;
    }
​
    Write_LCD_command ( gaddress, 1 ); /* 设定数据地址 */
    Write_LCD_data ( gdata );
}
​
/* 参数x是起始地址横坐标，y是起始地址纵坐标，gdata是要显示的数字 */
void DispNum_XY_LCD ( unsigned char x, unsigned char y, unsigned char gdata ) {
    unsigned char gaddress;
​
    if ( !y ) {
        gaddress = 0x80 + x;
    } else {
        gaddress = 0xc0 + x;
    }
​
    Write_LCD_command ( gaddress, 1 ); /* 设定数据地址 */
    Write_LCD_data ( gdata + 0x30 );
}
​
void Clear_Display ( void ) {
    Write_LCD_command ( 0x01, 1 );
    Delay_Ms ( 5 );
}
```

&emsp;&emsp;`LCD.h`如下：

``` c
#ifndef __LCD_H__
#define __LCD_H__
​
#define TESTBIT(a, b) ((a) & (1 << (b)))
​
#define LCD_RS_Low()  LPC_GPIO2->DATA &= ~(1<<8)  /* 给P2.8位写0  */
#define LCD_RS_High() LPC_GPIO2->DATA |=  (1<<8)  /* 给P2.8位写1  */
#define LCD_RW_Low()  LPC_GPIO2->DATA &= ~(1<<9)  /* 给P2.9位写0  */
#define LCD_RW_High() LPC_GPIO2->DATA |=  (1<<9)  /* 给P2.9位写1  */
#define LCD_EN_Low()  LPC_GPIO2->DATA &= ~(1<<10) /* 给P2.10位写0 */
#define LCD_EN_High() LPC_GPIO2->DATA |=  (1<<10) /* 给P2.10位写1 */
​
void Set_GPIO_Output ( void );
void Set_GPIO_Input ( void );
void LCD_GPIO_Init ( void );
void Write_Data_Port ( unsigned char Data );
unsigned char Read_Data_Port ( void );
unsigned char Chk_LCD_busy ( void );
void Write_LCD_command ( unsigned char gcmd, unsigned char gvalue );
void Write_LCD_data ( unsigned char gdata );
void INIT_LCD ( void );
void Display_LCD_string (
    unsigned char gadd_start, unsigned char gline,
    unsigned char glength, const unsigned char *pdata );
void DispChar_XY_LCD ( unsigned char x, unsigned char y, unsigned char gdata );
void DispNum_XY_LCD ( unsigned char x, unsigned char y, unsigned char gdata );
void Clear_Display ( void );
​
#endif
```