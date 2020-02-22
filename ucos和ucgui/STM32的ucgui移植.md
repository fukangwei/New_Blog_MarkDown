---
title: STM32的ucgui移植
categories: ucos和ucgui
date: 2019-03-19 09:18:14
---
&emsp;&emsp;这里移植的是`UCGUI3.90a`版本，虽然已经有更新的版本，例如`UCGUI3.98`、甚至`4.04`版本，但目前只有这个版本的代码是最全的，包括`JPEG`、`MULTILAYER`、`MEMDEV`、`AntiAlias`等模块。<!--more-->
&emsp;&emsp;`UCGUI`的文件数量很大，主要用到`UCGUI390a/Start/Config`和`UCGUI390a/Start/GUI`两个文件夹下文件，相关文件介绍如下。将`Config`和`GUI`下的所有文件加入工程，这是`UCGUI`官方推荐的结构：

目录               | 内容
-------------------|---------
`Config`           | 配置文件
`GUI/AntiAlias`    | 抗锯齿支持
`GUI/ConvertMono`  | 用于`B/W`(黑白两色)以及灰度显示的色彩转换程序
`GUI/ConvertColor` | 用于彩色显示的色彩转换程序
`GUI/Core`         | UCGUI内核文件
`GUI/Font`         | 字体文件
`GUI/LCDDriver`    | LCD驱动
`GUI/Mendev`       | 存储器件支持
`GUI/Touch`        | 触摸屏支持
`GUI/Widget`       | 视窗控件库
`GUI/WM`           | 视窗管理器

&emsp;&emsp;`JPEG`、`MemDev`、`MultiLayer`、`Widget`和`Wm`这`5`个文件夹的内容可以暂时不加入`MDK`工程，因为这些文件起到的是扩展功能。`ConverMono`、`ConverColor`、`Core`、`Font`这四个目录下的文件是不用修改的，要修改的文件在`LCDDriver`、`Config`这两个目录下的内容。`LCDDriver`是`LCD`的驱动接口函数文件，需要将自己的`LCD`驱动函数提供给`UCGUI`调用。需要提供`3`个`LCD`底层驱动函数：

- `void LCD_L0_SetPixelIndex ( int x, int y, int PixelIndex )`：`LCD`画点函数，用指定颜色填充一个像素。
- `unsigned int LCD_L0_GetPixelIndex ( int x, int y )`：`LCD`读取定点颜色函数，读取一个像素点的`16`位`RGB`颜色值。
- `void LCD_L0_FillRect ( int x0, int y0, int x1, int y1 )`：矩形填充函数，用指定颜色填充一个矩形。这个函数也可以不改，使用`UCGUI`的函数，用一个一个的像素点填充成一个矩形。也可以在底层驱动根据像素个数直接往`GRAM`中写数据，封装成函数，供这个函数调用，速度会快很多。

&emsp;&emsp;`LCDDriver`下有三个文件，即`LCDDummy.c`、`LCDNull.c`和`LCDWin.c`，它们都是`UCGUI`的`LCD`接口模板文件。以`LCDDummy.c`为例：

``` cpp
#include "LCD_Private.h" /* private modul definitions & config */
#include "GUI_Private.h"
#include "GUIDebug.h"

/* #if (LCD_CONTROLLER == -1) \
    && (!defined(WIN32) | defined(LCD_SIMCONTROLLER)) */ /* 必须注释，否则不会编译 */
#include "ili93xx.h" /* 包含你的LCD驱动函数声明 */
#if (LCD_CONTROLLER == -1) /* 这句对应“Config/LCDConf.h” */

void LCD_L0_SetPixelIndex ( int x, int y, int PixelIndex ) {
    POINT_COLOR = PixelIndex; /* 我的画点函数使用了一个全局变量设定颜色 */
    LCD_DrawPoint ( x, y ); /* 画点函数 */
}

unsigned int LCD_L0_GetPixelIndex ( int x, int y ) {
    return LCD_ReadPoint ( x, y ); /* 我的读取像素颜色函数 */
}

void LCD_L0_FillRect ( int x0, int y0, int x1, int y1 ) {
    LCD_Fill ( x0, y0, x1, y1, LCD_COLORINDEX ); /* 填充矩形函数 */
    /*------------------------
    for ( ; y0 <= y1; y0++ ) {
        LCD_L0_DrawHLine ( x0, y0, x1 );
    }
    ------------------------*/
}
```

`UCGUI`提供了一些`LCD`控制器的驱动函数，但是这种配置方法可以适用于任何控制`IC`。
&emsp;&emsp;接下来修改`Config`文件夹下文件，即`GUIConf.h`、`LCDConf.h`和`GUITouchConf.h`。还需要加入一个`GUI_X.c`文件，否则编译时会有错误，直接复制`UCGUI390a\Sample\GUI_X\GUI_X.c`即可。如果打开了触摸功能，还需要加入`UCGUI390a\Sample\GUI_X\GUI_X_Touch.c`。这三个文件是`UCGUI`的上层配置文件，也就是`GUI`一些功能的开关。
&emsp;&emsp;`GUIConf.h`如下：

``` cpp
#ifndef GUICONF_H
#define GUICONF_H

#define GUI_OS              (0) /* 操作系统的支持，当用到ucos时需要打开 */
#define GUI_SUPPORT_TOUCH   (0) /* 触摸屏的支持 */
#define GUI_SUPPORT_UNICODE (0) /* 用汉字库时再打开 */

#define GUI_DEFAULT_FONT &GUI_Font6x8 /* 定义字体大小 */
#define GUI_ALLOC_SIZE   12500 /* 分配的动态内存空间 */

/*********************************************************************
*
*        Configuration of available packages
*/
#define GUI_WINSUPPORT     0 /* 窗口功能支持  要使用指针图标时必须打开 */
#define GUI_SUPPORT_MEMDEV 0 /* 内存管理 */
#define GUI_SUPPORT_AA     0 /* 抗锯齿功能，打开后可以提高显示效果 */

#endif /* Avoid multiple inclusion */
```

&emsp;&emsp;`LCDConf.h`如下：

``` cpp
#ifndef LCDCONF_H
#define LCDCONF_H

#define LCD_XSIZE (320) /* lcd的水平分辨率 */
#define LCD_YSIZE (480) /* lcd的垂直分辨率 */

#define LCD_BITSPERPIXEL (16) /* 16位颜色RGB值(颜色深度) */
#define LCD_SWAP_RB      (1)  /* 红蓝反色交换 */

/* lcd控制器的具体型号
 *
 * 设置为“-1”时，会编译LCDDriver下的LCDDummy.c
 * 设置为“-2”时，会编译LCDDriver下的LCDNull.c
 *
 * 还需要修改LCDDriver下文件的宏定义才可以被编译
 * eg. LCDDummy.c：
 *
 * #if (LCD_CONTROLLER == -1) && (!defined(WIN32) |defined(LCD_SIMCONTROLLER))
 * 改为#if (LCD_CONTROLLER == -1)
 */
#define LCD_CONTROLLER  -1 /* 设置为“-1”或“-2”，因为UCGUI没有相应LCD控制IC驱动 */
#define LCD_INIT_CONTROLLER() LCD_Config(); /* 绑定相关LCD底层驱动的初始化函数 */
```

配置完这两个文件，如果不启用触摸屏的话，UCGUI已经可以正常运行。
&emsp;&emsp;先修改主函数，添加`#include "GUI.h"`，在`main`里添加：

``` cpp
GUI_Init();
GUI_SetBkColor ( GUI_BLUE );
GUI_SetColor ( GUI_RED );
GUI_Clear();
GUI_DrawCircle ( 100, 100, 50 ); /* 画圆 */

while ( 1 );
```

要实现横屏效果，首先让显示屏基本驱动实现横屏，然后将`LCDConf.h`中的`LCD_XSIZE`和`LCD_YSIZE`进行互换。
&emsp;&emsp;下面讲解`UCGUI`触摸屏的配置。在`ucgui`的`Config`目录中添加`GUI_X_Touch.c`，然后将`GUIConf.h`文件中的`GUI_WINSUPPORT`和`GUI_SUPPORT_TOUCH`设置为`1`。配置`GUIToucConf.h`文件(下面的配置是针对`320 * 240`的`2.8`寸`TFT LCD`，上面的是针对`480 * 320`的`TFT LCD`)：

``` cpp
#ifndef GUITOUCH_CONF_H
#define GUITOUCH_CONF_H

#define GUI_TOUCH_AD_LEFT   0
#define GUI_TOUCH_AD_RIGHT  240
#define GUI_TOUCH_AD_TOP    0
#define GUI_TOUCH_AD_BOTTOM 320

#define GUI_TOUCH_SWAP_XY  0
#define GUI_TOUCH_MIRROR_X 0
#define GUI_TOUCH_MIRROR_Y 0

#endif
```

`UCGUI`触摸屏驱动接口函数文件`GUI_X_Touch.c`：

``` cpp
#include "GUI.h"
#include "GUI_X.h"
#include "touch.h"

void GUI_TOUCH_X_ActivateX ( void ) {
}

void GUI_TOUCH_X_ActivateY ( void ) {
}

int GUI_TOUCH_X_MeasureX ( void ) {
    Convert_Pos();
    return Pen_Point.X0;
}

int GUI_TOUCH_X_MeasureY ( void ) {
    Convert_Pos();
    return Pen_Point.Y0;
}
```

对`main`函数进行如下修改：

``` cpp
#include "led.h"
#include "delay.h"
#include "sys.h"
#include "usart.h"
#include "GUI.h"
#include "touch.h"

int main ( void ) {
    SystemInit();
    delay_init ( 72 );
    NVIC_Configuration();
    uart_init ( 9600 );
    LED_Init();
    GUI_Init();
    Touch_Init();
    GUI_SetBkColor ( GUI_RED ); /* 设置背景颜色 */
    GUI_SetColor ( GUI_WHITE ); /* 设置前景颜色，即字体和绘图的颜色 */
    GUI_Clear(); /* 按指定颜色清屏 */
    GUI_DispStringAt ( "Hello World ..", 10, 10 ); /* 显示字符 */
    GUI_CURSOR_Show(); /* 显示鼠标来测试触摸屏，必须打开窗口功能GUI_WINSUPPORT */

    while ( 1 ) {
        GUI_TOUCH_Exec(); /* 调用UCGUI的TOUCH相关函数 */
        GUI_Exec(); /* GUI事件更新 */
    }
}
```

&emsp;&emsp;触摸屏横屏的方法如下：
&emsp;&emsp;1. 修改`LCD`的默认显示方向：`LCD`的驱动文件里有个`LCD_MyInit`函数，用于初始化`LCD`。该函数中有个`LCD_Display_Dir`函数，用于切换`LCD`的显示方向，根据需要选择：

``` cpp
LCD_Display_Dir ( 1 ); /* 0为竖屏，1为横屏 */
```

&emsp;&emsp;2. 修改`ucgui`中对应于触摸的坐标检测函数：`GUI_X_Touch.c`有两个检测触摸的函数，即`GUI_TOUCH_X_MeasureX`与`GUI_TOUCH_X_MeasureY`。如果是竖屏显示，`GUI_TOUCH_X_MeasureX`返回`X`坐标，`GUI_TOUCH_X_MeasureY`返回`Y`坐标，横屏显示的话反过来就可以了：

``` cpp
int GUI_TOUCH_X_MeasureX ( void ) {
    tp_dev.scan ( 0 );
    // return tp_dev.x; /* 竖屏显示 */
    return tp_dev.y; /* 横屏显示 */
}

int GUI_TOUCH_X_MeasureY ( void ) {
    tp_dev.scan ( 0 );
    // return tp_dev.y; /* 竖屏显示 */
    return tp_dev.x; /* 横屏显示 */
}
```

&emsp;&emsp;3. 修改`ucgui`中触摸屏的相关设置：在`LCDConf.h`中修改`LCD_XSIZE`与`LCD_YSIZE`，在`GUITouchConf.h`中修改`GUI_TOUCH_AD_RIGHT`与`GUI_TOUCH_AD_BOTTOM`。如果`X`轴触摸是反的，需要修改一下`GUI_TOUCH_MIRROR`。
&emsp;&emsp;如果需要将`UCOS`系统移植到`ucgui`上，可以参考以下步骤：
&emsp;&emsp;1. 首先将`ucos`系统移植到`STM32`上，并通过信号量、消息邮箱等机制的测试。
&emsp;&emsp;2. 将`GUIConf.h`文件中的`GUI_OS`设置为`1`，同时在`Config`文件夹下添加`GUI_X_uCOS.c`文件，并加入到工程中。
&emsp;&emsp;3. 将`GUI_X_uCOS.c`文件中的`GUI_X_ExecIdle`函数改为：

``` cpp
void GUI_X_ExecIdle ( void ) {
    //OS_X_Delay ( 1 );
    OSTimeDly ( 50 );
}
```

同时在该文件中增加宏定义：

``` cpp
#define TRUE  1
#define FALSE 0
```

将`GUI_X.c`文件中的如下`4`个函数注释掉：

- `GUI_X_GetTime(void);`
- `GUI_X_Delay(int ms);`
- `GUI_X_Init(void);`
- `GUI_X_ExecIdle(void);`

最后将`TFT`显示屏中的延时函数用`ucos`的延时函数替代，延时毫秒的(如`delay_ms`)用`OSTimeDly`替代，延时微秒的(如`delay_us`)用`OSTimeDly(1);`替代。至此所有移植工作结束！