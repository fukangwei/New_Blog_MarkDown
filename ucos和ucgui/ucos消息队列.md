---
title: ucos消息队列
categories: ucos和ucgui
abbrlink: 42ac754c
date: 2018-12-29 11:27:56
---
&emsp;&emsp;代码如下：<!--more-->

``` cpp
#include "includes.h"
​
#define TASK_STK_SIZE 512
#define N_MESSAGES    128
​
OS_STK StartTaskStk[TASK_STK_SIZE];
OS_STK MyTaskStk[TASK_STK_SIZE];
OS_STK YouTaskStk[TASK_STK_SIZE];
​
char *s_flag; /* 该字符串指示哪个任务在运行 */
char *ss; /* 存放接收到的消息指针 */
char *s100; /* 存放发送消息的指针 */
char *s;
char *s500;
/* 创建消息队列，首先需要定义一个指针数组(用于存放消息邮箱)，然后把各个消息数据
   缓冲区的首地址存入这个数组中，最后再调用函数OSQCreate来创建消息队列 */
void *MsgGrp[N_MESSAGES]; /* 定义消息指针数组 */
INT8U err;
INT8U y = 0;
OS_EVENT *Str_Q; /* 定义事件控制块指针。队列的事件控制块指针用于存放创建的消息队列的指针 */
​
void MyTask ( void *data );
void StartTask ( void *data );
void YouTask ( void *data );
​
void main ( void ) {
    OSInit();
    PC_DOSSaveReturn();
    PC_VectSet ( uCOS, OSCtxSw );
    Str_Q = OSQCreate ( &MsgGrp[0], N_MESSAGES ); /* 创建消息队列 */
    /* 函数的第一个参数“&MsgGrp[0]”是“void **start”，是存放消息缓冲区指针数组的地址。
       它是指向指针数组的指针，可以用指针数组的首个元素的地址表示 */
    /* N_MESSAGES是该数组的大小，返回值是消息队列的指针。Str_Q是OS_EVENT型的指针，是事件控制块型的指针 */
    OSTaskCreate ( StartTask, ( void * ) 0, &StartTaskStk[TASK_STK_SIZE - 1], 0 );
    OSStart();
}
​
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
    OSTaskCreate ( MyTask, ( void * ) 0, &MyTaskStk[TASK_STK_SIZE - 1], 3 );
    OSTaskCreate ( YouTask, ( void * ) 0, &YouTaskStk[TASK_STK_SIZE - 1], 4 );
​
    // s = "How many strings could be geted?";
    // /* 发送消息，以LIFO后进先出的方式发送。第一个参数Str_Q是消息队列的指针，
    //    是OSQCreate的返回值，第二个参数s是消息指针 */
    // OSQPostFront( Str_Q, s );
    for ( ;; ) {
        s_flag = "The StartTask is running!";
        PC_DispStr ( 50, ++y, s_flag, DISP_FGND_RED + DISP_BGND_LIGHT_GRAY ); /* 提示哪个任务在运行 */
​
        if ( OSTimeGet() > 100 && OSTimeGet() < 500 ) {
            s100 = "The value of OSTIME is from 100 to 500 NOW!!";
            OSQPostFront ( Str_Q, s100 ); /* 发送消息，以LIFO后进先出的方式发送 */
            s = "The string belongs to which task.";
            OSQPostFront ( Str_Q, s ); /* 发送消息，以LIFO方式发送。所以如果要申请消息时，会先得到s，然后才是s100 */
        }
​
        if ( OSTimeGet() > 1000 && OSTimeGet() < 1500 ) {
            s500 = "The value of OSTIME is from 1000 to 1500 NOW!!";
            OSQPostFront ( Str_Q, s500 ); /* 发送消息 */
        }
​
        if ( PC_GetKey ( &key ) == TRUE ) {
            if ( key == 0x1B ) {
                PC_DOSReturn();
            }
        }
​
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
void MyTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;
​
    for ( ;; ) {
        s_flag = "The MyTask is running!";
        PC_DispStr ( 50, ++y, s_flag, DISP_FGND_RED + DISP_BGND_LIGHT_GRAY ); /* 提示哪个任务在运行 */
        /* 请求消息队列，参数分别是：Str_Q为所请求消息队列的指针，第二个参数为等待时间 */
        /* 0表示无限等待，&err为错误信息，返回值为队列控制块OS_Q成员OSQOut指向的消息(如果队列中有消息可用的话)，
           如果没有消息可用，在使调用OSQPend的任务挂起，使之处于等待状态，并引发一次任务调度。
           因为前面发送消息时使用的是LIFO的方式，所以此处第一次得到的消息是上面最后发送的消息 */
        ss = OSQPend ( Str_Q, 0, &err );
        PC_DispStr ( 3, y, ss, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY ); /* 显示得到的消息 */
        PC_DispStr ( 0, y, "My", DISP_FGND_RED + DISP_BGND_LIGHT_GRAY ); /* 显示是哪一个任务显示的 */
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
void YouTask ( void *pdata ) {
#if OS_CRITICAL_METHOD == 3
    OS_CPU_SR cpu_sr;
#endif
    pdata = pdata;
​
    for ( ;; ) {
        s_flag = "The YouTask is running!";
        PC_DispStr ( 50, ++y, s_flag, DISP_FGND_RED + DISP_BGND_LIGHT_GRAY ); /* 提示哪个任务在运行 */
        ss = OSQPend ( Str_Q, 0, &err ); /* 请求消息队列 */
        PC_DispStr ( 3, y, ss, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY ); /* 显示得到的消息 */
        PC_DispStr ( 0, y, "You", DISP_FGND_RED + DISP_BGND_LIGHT_GRAY ); /* 显示是哪一个任务显示的 */
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
```