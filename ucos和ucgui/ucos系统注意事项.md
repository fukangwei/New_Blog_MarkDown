---
title: ucos系统注意事项
date: 2018-12-29 11:56:29
categories: ucos和ucgui
---
1. `ucos`系统的底层一定要移植好，否则会出现信号量、消息邮箱失效等现象，甚至死机。移植好的系统要经过信号量、消息邮箱等测试，才能放心使用。
2. 消息邮箱的创建函数一定要放在`ucos`系统初始化函数的后面，否则邮箱机制失效。
3. 使用信号量、消息邮箱等机制时，一定要注意任务的优先级。
4. 移植`ucos`系统后，要将设备驱动的延时函数用`ucos`的延时函数进行替代，设备的初始化函数放在任务中，否则会出现死机现象。
5. 使用`ucos`系统时，如果出现死机现象，可以考虑调整任务的优先级。
6. 如果芯片的内存足够的话，尽量把函数中的局部数组改为静态局部数组(加上`static`关键字)，这样可以避免堆栈溢出，尤其是在使用`ucos`操作系统的时候。
7. 使用`ucos`系统时，如果系统中有包含`delay`的函数(例如`W5500`的硬重启函数)和`ucos`的系统时钟初始化函数，先使用包含`delay`的函数，因为`stm32`的`delay`函数也是使用了系统时钟。如果不注意顺序，会产生冲突。
8. 注意，`ucos`的延时函数不要在非任务(即不是`Task`)的部分中运行。
9. 在`ucos`的非任务(即不是`Task`)部分不要使用`printf`函数。在任务部分可以使用`printf`函数，但要注意`8`字节对齐。
10. 在`ucos`的每一个任务(`Task`)中，必须要有一个类似于`OSTimeDlyHMSM`的函数，并且该函数必须要在任务的每一次轮询中产生作用。
11. 在使用`ucos`时，不要将任务的优先级安排得太紧密，中间留一些间隔比较好。
12. `ucos`系统在`STM32`上打印浮点数的时候会出现错误，最好让每一个任务的堆栈都以`8`字节对齐。
13. 如果需要使用`ucos`的邮箱向多个任务发送信息，可以使用`OSMboxPostOpt`函数。

### UCOS的printf浮点数问题

&emsp;&emsp;当使用`ucos`时，`printf`、`sprintf`打印浮点数会出现问题，但当类型是`int`或`short`时没有问题。根据网上资料，将任务堆栈设置为`8`字节对齐就可以了。当没有操作系统时，系统堆栈是`8`字节对齐的；但是当使用`ucos`时，用户任务不一定是`8`字节对齐。
&emsp;&emsp;如果是`IAR`编译器，通过`#pragma data_alignment`指定对齐字节数：

``` c
#pragma data_alignment=8
OS_STK Task1_LED1_Stk[Task1_LED1_Stk_Size];
#pragma data_alignment=8
OS_STK Task2_backlight_Stk[Task2_backlight_Stk_Size];
```

&emsp;&emsp;如果使用`MDK`编译器，请在系统任务堆栈前面进行数据对齐声明：

``` c
__align ( 8 ) static OS_STK TaskStartStk[TASK_START_STK_SIZE];
```