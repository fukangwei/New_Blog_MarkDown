---
title: ucos信号量集
categories: ucos和ucgui
abbrlink: ae48ceda
date: 2018-12-29 11:48:15
---
&emsp;&emsp;信号量集又称`事件标志组`，代码如下：<!--more-->

``` cpp
#include "INCLUDES.h"
​
#define TASK_STK_SIZE 512
​
static OS_STK StartTaskStk[TASK_STK_SIZE]; /* 起始任务 */
static OS_STK MyTaskStk[TASK_STK_SIZE];
static OS_STK YouTaskStk[TASK_STK_SIZE];
static OS_STK HerTaskStk[TASK_STK_SIZE];
​
char *s1 = "Mytask is running";
char *s2 = "Youtask is running";
char *s3 = "Hertask is running";
​
INT8U err; /* 返回的错误信息 */
INT8U y = 0; /* 字符显示位置 */
OS_FLAG_GRP *Sem_F; /* 定义一个信号量集指针，是标志组类型。OS_FLAG_GRP类型的指针，用标志组描述信号量集 */
​
void StartTask ( void *data );
void MyTask ( void *data );
void YouTask ( void *data );
void HerTask ( void *data );
​
void main ( void ) {
    OSInit();
    PC_DOSSaveReturn();
    PC_VectSet ( uCOS, OSCtxSw );
    /* 创建信号量集，函数的原型为：OS_FLAG_GRP *OSFlagCreate(OS_FLAGS flags, INT8U *err) */
    /* 参数OS_FLAGS flags是信号的初始值，在这里指定为0，即信号初始值为0。参数“*err”是错误信息，前面已经定义了。
       返回值为OS_FLAG_GRP型的指针，即为创建的信号量集的标志组的指针，前面已经定义了 */
    Sem_F = OSFlagCreate ( 0, &err );
    OSTaskCreate ( StartTask, ( void * ) 0, &StartTaskStk[TASK_STK_SIZE - 1], 0 ); /* 创建起始任务 */
    OSStart();
}
​
void StartTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    INT16S key;
    pdata = pdata;
    OS_ENTER_CRITICAL(); /* 进入临界段 */
    PC_VectSet ( 0x08, OSTickISR );
    PC_SetTickRate ( OS_TICKS_PER_SEC );
    OS_EXIT_CRITICAL(); /* 退出临界段 */
    OSStatInit();
    /* 在起始任务中创建三个任务 */
    OSTaskCreate ( MyTask, ( void * ) 0, &MyTaskStk[TASK_STK_SIZE - 1], 3 );
    OSTaskCreate ( YouTask, ( void * ) 0, &YouTaskStk[TASK_STK_SIZE - 1], 4 );
    OSTaskCreate ( HerTask, ( void * ) 0, &HerTaskStk[TASK_STK_SIZE - 1], 5 );
​
    for ( ;; ) {
        if ( PC_GetKey ( &key ) == TRUE ) {
            if ( key == 0x1B ) { /* 如果按下ESC键，则退出UC/OS-II */
                PC_DOSReturn();
            }
        }
​
        OSTimeDlyHMSM ( 0, 0, 3, 0 );
    }
}
​
void MyTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;
​
    for ( ; ; ) {
        OSFlagPend ( /* 请求信号量集 */
            Sem_F, /* 请求信号量集指针 */
            /* 过滤器，请求第0和第1位信号，即0011。这里是把数据3强制转化为OS_FLAGS
               类型的数据，因为过滤器和信号量集中的信号都是OS_FLAGS类型的数据 */
            ( OS_FLAGS ) 3,
            /* “OS_FLAG_WAIT_SET_ALL + OS_FLAG_CONSUME”的意思是信号全是1时有效，
               参数OS_FLAG_CONSUME表示当任务等待的事件发生后，清除相应的事件标志位 */
            OS_FLAG_WAIT_SET_ALL, /* 只有当信号全是1时信号有效，没有加参数OS_FLAG_CONSUME，所以不会清除标志位 */
            0, /* 表示等待时限，0表示无限等待 */
            &err /* 错误信息 */
        );
        /* 任务MyTask在这里请求信号量集，如果请求到了信号量集，就继续运行，在下面显示信息；
           如果请求不到信号量集，MyTask就挂起，处于等待状态，只到请求到了信号量集才继续往下运行 */
        PC_DispStr ( 10, ++y, s1, DISP_BGND_BLACK + DISP_FGND_WHITE ); /* 显示信息 */
        OSTimeDlyHMSM ( 0, 0, 2, 0 ); /* 等待2s */
    }
}
​
void YouTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;
​
    for ( ; ; ) {
        PC_DispStr ( 10, ++y, s2, DISP_BGND_BLACK + DISP_FGND_WHITE ); /* 显示信息 */
        OSTimeDlyHMSM ( 0, 0, 8, 0 ); /* 等待8s */
        OSFlagPost ( /* 向信号量集发信号 */
            Sem_F, /* 发送信号量集的指针 */
            /* 选择要发送的信号，给第1位发信号，即0010。同样把2强制转化为OS_FLAGS型的数据，因为信号为OS_FLAGS型的 */
            ( OS_FLAGS ) 2,
            OS_FLAG_SET, /* 信号有效的选项，信号置1。OS_FLAG_SET为置1，OS_FLAG_CLR为置0 */
            &err /* 错误信息 */
        );

        OSTimeDlyHMSM ( 0, 0, 2, 0 ); /* 等待2s */
    }
}
​
void HerTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;
​
    for ( ; ; ) {
        PC_DispStr ( 10, ++y, s3, DISP_BGND_BLACK + DISP_FGND_WHITE ); /* 显示信息 */
        OSTimeDlyHMSM ( 0, 0, 8, 0 ); /* 等待8s */
        OSFlagPost ( /* 向信号量集发信号 */
            Sem_F,
            ( OS_FLAGS ) 1, /* 给第0位发信号，即0001，把1强制转化为OS_FLAGS型 */
            OS_FLAG_SET, /* 信号置1 */
            &err
        );

        OSTimeDlyHMSM ( 0, 0, 1, 0 ); /* 等待1s */
    }
}
```