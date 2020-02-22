---
title: ucos内存管理
categories: ucos和ucgui
date: 2018-12-29 11:22:58
---
&emsp;&emsp;代码如下：<!--more-->

``` cpp
#include "INCLUDES.h"

#define TASK_STK_SIZE 512

OS_STK StartTaskStk[TASK_STK_SIZE];
OS_STK MyTaskStk[TASK_STK_SIZE];
OS_STK YouTaskStk[TASK_STK_SIZE];
OS_STK HerTaskStk[TASK_STK_SIZE];

char *s = NULL;
char *s1 = "Mytask  ";
char *s2 = "Youtask ";
char *s3 = "Hertask ";
INT8U err; /* 错误信息 */
INT8U y = 0; /* 字符显示位置 */
INT8U Times = 0;
OS_MEM *IntBuffer; /* 定义内存控制块指针，创建一个内存分区时，返回值就是它 */
INT8U IntPart[8][6]; /* 划分一个具有8个内存块，每个内存块长度是6个字节的内存分区 */
INT8U *IntBlkPtr; /* 定义内存块指针(INT8U型) */
/* 存放内存分区的状态信息。函数OSMemQuery查询到的动态内存分区状态信息是一个
   SO_MEM_DATA型的数据结构。查询到的内存分区的有关信息就放在这个数据结构中 */
OS_MEM_DATA MemInfo;
void StartTask ( void *data );
void MyTask ( void *data );
void YouTask ( void *data );
void HerTask ( void *data );

void main ( void ) {
    OSInit();
    PC_DOSSaveReturn();
    PC_VectSet ( uCOS, OSCtxSw );
    IntBuffer = OSMemCreate ( IntPart, 8, 6, &err ); /* 创建动态内存区 */
    OSTaskCreate ( StartTask, ( void * ) 0, &StartTaskStk[TASK_STK_SIZE - 1], 0 ); /* 创建起始函数 */
    OSStart();
}

void StartTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    INT16S key;
    pdata = pdata;
    OS_ENTER_CRITICAL();
    PC_VectSet ( 0x08, OSTickISR );
    PC_SetTickRate ( OS_TICKS_PER_SEC );
    OS_EXIT_CRITICAL();
    OSStatInit();
    OSTaskCreate ( MyTask, ( void * ) 0, &MyTaskStk[TASK_STK_SIZE - 1], 3 ); /* 创建任务 */
    OSTaskCreate ( YouTask, ( void * ) 0, &YouTaskStk[TASK_STK_SIZE - 1], 4 );
    OSTaskCreate ( HerTask, ( void * ) 0, &HerTaskStk[TASK_STK_SIZE - 1], 5 );

    for ( ;; ) {
        if ( PC_GetKey ( &key ) == TRUE ) {
            if ( key == 0x1B ) { /* 如果按下ESC键，则退出UC/OS-II */
                PC_DOSReturn();
            }
        }

        OSTimeDlyHMSM ( 0, 0, 3, 0 );
    }
}

void MyTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;

    for ( ; ; ) {
        PC_DispStr ( 10, ++y, s1, DISP_BGND_BLACK + DISP_FGND_WHITE ); /* 显示信息 */
        IntBlkPtr = OSMemGet ( /* 请求内存块*/
                        IntBuffer, /* 内存分区的指针 */
                        &err /* 错误信息 */ );
        OSMemQuery ( /* 查询内存控制块信息 */
            IntBuffer, /* 带查询内存控制块指针 */
            &MemInfo );
        sprintf ( s, "%0x", MemInfo.OSFreeList ); /* 显示头指针，把得到的空闲内存块链表首地址的指针放到指针s所指的空间中 */
        PC_DispStr ( 30, y, s, DISP_BGND_BLACK + DISP_FGND_WHITE ); /* 把空闲内存块链表首地址的指针显示出来 */
        sprintf ( s, "%d", MemInfo.OSNUsed ); /* 显示已用的内存块数目 */
        PC_DispStr ( 40, y, s, DISP_BGND_BLACK + DISP_FGND_WHITE );

        if ( Times >= 5 ) { /* 运行六次后 */
            OSMemPut ( /* 释放内存块函数 */
                IntBuffer, /* 内存块所属内存分区的指针 */
                IntBlkPtr /* 待释放内存块指针 */
                /* 此次释放，只能释放最后一次申请到的内存块，前面因为IntBlkPtr被后面的给覆盖掉了，所以释放不了 */
            );
        }

        Times++; /* 运行次数加1 */
        OSTimeDlyHMSM ( 0, 0, 1, 0 ); /* 等待1s */
    }
}

void YouTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;

    for ( ; ; ) {
        PC_DispStr ( 10, ++y, s2, DISP_BGND_BLACK + DISP_FGND_WHITE );
        IntBlkPtr = OSMemGet ( /* 请求内存块 */
                        IntBuffer, /* 内存分区的指针 */
                        &err /* 错误信息 */);
        OSMemQuery ( /* 查询内存控制块信息 */
            IntBuffer, /* 待查询内存控制块指针 */
            &MemInfo );
        sprintf ( s, "%0x", MemInfo.OSFreeList ); /* 显示头指针 */
        PC_DispStr ( 30, y, s, DISP_BGND_BLACK + DISP_FGND_WHITE );
        sprintf ( s, "%d", MemInfo.OSNUsed ); /* 显示已用的内存块数目 */
        PC_DispStr ( 40, y, s, DISP_BGND_BLACK + DISP_FGND_WHITE );
        OSMemPut ( /* 释放内存块 */
            IntBuffer, /* 内存块所属内存分区的指针 */
            IntBlkPtr /* 待释放内存块指针 */ );
        OSTimeDlyHMSM ( 0, 0, 2, 0 ); /* 等待2s */
    }
}

void HerTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;

    for ( ; ; ) {
        PC_DispStr ( 10, ++y, s3, DISP_BGND_BLACK + DISP_FGND_WHITE );
        IntBlkPtr = OSMemGet ( /* 请求内存块 */
                        IntBuffer, /* 内存分区的指针 */
                        &err /* 错误信息 */ );
        OSMemQuery ( /* 查询内存控制块信息 */
            IntBuffer, /* 待查询内存控制块指针 */
            &MemInfo );
        sprintf ( s, "%0x", MemInfo.OSFreeList ); /* 显示头指针 */
        PC_DispStr ( 30, y, s, DISP_BGND_BLACK + DISP_FGND_WHITE );
        sprintf ( s, "%d", MemInfo.OSNUsed ); /* 显示已用的内存块数目 */
        PC_DispStr ( 40, y, s, DISP_BGND_BLACK + DISP_FGND_WHITE );
        OSMemPut (
            IntBuffer, /*内存块所属内存分区的指针 */
            IntBlkPtr /* 待释放内存块指针 */ );
        OSTimeDlyHMSM ( 0, 0, 1, 0 ); /* 等待1s */
    }
}
```