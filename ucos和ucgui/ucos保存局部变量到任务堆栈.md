---
title: ucos保存局部变量到任务堆栈
categories: ucos和ucgui
date: 2018-12-29 09:49:02
---
&emsp;&emsp;1. 没有`OS`时，任务如何保存局部变量？<!--more-->
&emsp;&emsp;在我的知识体系里，我一直以为单片机中就只有一个栈。以`stm32`为例，在启动文件中有这么一段代码：

``` cpp
; Amount of memory (in bytes) allocated for Stack
; Tailor this value to your application needs
; <h> Stack Configuration
; <o> Stack Size (in Bytes) <0x0-0xFFFFFFFF:8>
; </h>

Stack_Size    EQU     0x00000400
AREA   STACK, NOINIT, READWRITE, ALIGN=3
Stack_Mem   SPACE   Stack_Size
__initial_sp
```

假设`STM32`的内存有`16KB`，从启动文件中可以看到，栈的大小为`0x400`，我称之为`系统栈`，系统栈的范围为`0x20000000`至`0x20000400`。在没有`OS`的应用中，`CPU`其实有两个任务，一个是`中断任务`，一个是`main`函数中的任务。当函数调用或者发生中断时，就使用系统栈保存局部变量和寄存器状态，也就是`SP`指向`0x20000000`至`0x20000400`。
&emsp;&emsp;2. `ucos`中如何保存局部变量？
&emsp;&emsp;其实有没有操作系统都一样，都把任务中的局部变量和当前的寄存器状态保存在栈中。在`UCOS`中，一个任务就分配一个栈。假设有两个任务，申请分配对应的任务栈如下：

``` cpp
/* 假设任务1的堆栈地址为0x20000500，那么任务1中的局部变量和寄存器状态将保存在0x20000500至0x20000580 */
static OS_STK Task1Stk[128];
/* 假设任务1的堆栈地址为0x20000600，那么任务2中的局部变量和寄存器状态将保存在0x20000600至0x20000700 */
static OS_STK Task2Stk[256];
```

当任务`1`运行时，系统使用的栈不再是`0x20000000`至`0x20000400`，而是`0x20000500`至`0x20000580`；任务`2`运行时，系统中的栈指向`0x20000600`至`0x20000700`。那么如何切换`SP`呢？假设切换到任务`1`的`SP`：

``` cpp
; 保存任务1的SP
LDR R1, =Task1Stk ; 把SP存在R1中，在任务1中R1等于0x20000500至0x20000580
LDR R1, [R1]
STR R0, [R1] ; R0 is SP of process being switched out

; 切换到任务1的SP
MSR PSP, R0 ; Load PSP with new process SP
```

就这样，在`UCOS`中模拟系统栈，生成任务栈，局部变量在任务栈中的分配，而不在系统栈中。