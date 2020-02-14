---
title: ucgui仪表盘
categories: ucos和ucgui
abbrlink: 2dd4064
date: 2018-12-29 09:43:31
---
&emsp;&emsp;代码如下：<!--more-->

``` cpp
#include <stdlib.h>
#include "GUI.h"
#include "stdlib.h"
#include <math.h>
#include <stddef.h>
​
#define DEG2RAD (3.1415926f/180) /* 绘制弧度的转化 */
​
void _DrawScale ( void ) {
    int XSize = LCD_GetXSize(); /* 取得LCD物理尺寸，这个参数是在“lcdconf.h”里面定义 */
    int YSize = LCD_GetYSize();
    int XMide =  XSize / 2;
    int i = 0;
    int r1 = 110; /* 定义效果图中下面的一条弧的半径 */
    int r2 = 140; /* 定义效果图中上面的一条弧半径 */
    int rt = 100; /* 定义效果图中刻度(数字)所在弧的半径 */
    int step = 15;
    int r = ( r1 + r2 ) / 2;
    int c = 0;
    GUI_SetPenSize ( r2 - r1 ); /* 设置线型宽度为30 */
    GUI_SetColor ( GUI_RED ); /* 第一个扇形块填充红色 */
    GUI_AA_DrawArc ( XMide, YSize, r, r, 45, 60 );
    GUI_SetColor ( GUI_GREEN ); /* 第一个扇形块填充红色 */
    GUI_AA_DrawArc ( XMide, YSize, r, r, 60, 75 );
    GUI_SetColor ( GUI_WHITE );
    GUI_SetPenSize ( 5 ); /* 设置线性宽度为2，绘制上下两条圆弧 */
    GUI_AA_DrawArc ( XMide, YSize, r1, r1, 45, 135 ); /* 绘制下圆弧 */
    GUI_AA_DrawArc ( XMide, YSize, r2, r2, 45, 135 ); /* 绘制上圆弧 */
​
    for ( i = 45; i <= 135; i += step ) {
        float co = cos ( i * DEG2RAD );
        float si = sin ( i * DEG2RAD );
        int x1 = XMide - r1 * co;
        int y1 = YSize - r1 * si;
        int x2 = XMide - ( r2 - 1 ) * co;
        int y2 = YSize - ( r2 - 1 ) * si;
        int xt = XMide - rt * co;
        int yt = YSize - rt * si;
        GUI_SetColor ( GUI_WHITE );
        /* 绘制刻度线，共4个参数，x1和y1为直线起始坐标，x2和y2为直线终止坐标 */
        GUI_AA_DrawLine ( x1, y1, x2, y2 );
        GUI_SetColor ( GUI_GREEN );
        GUI_SetFont ( &GUI_Font8x8 );
​
        if ( c <= 1 ) {
            GUI_DispCharAt ( '0' + c * 7, xt - 4, yt - 4 );
        } else {
            GUI_DispDecAt ( c * 7, xt - 4, yt - 4, 2 );
        }
​
        c++;
    }
}

void MainTask ( void ) {
    GUI_Init();
    _DrawScale();
​
    while ( 1 );
}
```