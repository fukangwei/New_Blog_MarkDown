---
title: OSSemCreate函数
categories: ucos和ucgui
abbrlink: c16137d9
date: 2018-12-29 14:20:28
---
&emsp;&emsp;信号量在创建时，调用`OSSemCreate(INT16U cnt)`函数，`cnt`为信号量的初始值。对`cnt`赋予不同的值，所起到的作用不同。如果`Semp = OSSemCreate(0)`，表示该信号量等待一个事件或者多个事件的发生。
&emsp;&emsp;如果我们想对一个公共资源进行互斥访问，例如让两个任务`Task1`和`Task2`都可以调用`Fun`函数，但不能同时调用，最好定义`Semp = OSSemCreate(1)`。在各自的任务中都需要调用`OSSemPend(Semp, 0, &err)`请求此信号量。如果可用，则调用`Fun`，然后再调用`OSSemPost(Semp)`释放该信号量，这样就实现了对一个资源的互斥访问。对于`OSSemCreate(1)`，如果一个任务中有`OSSemPend`，那么可以执行，执行之后`cnt`等于`0`。其他任务的`OSSemPend`无法获得`sem`，只能等待，除非任务一有`OSSemPost`，使其`cnt`加`1`，这样其他任务的`OSSemPend`可以执行。同理，如果一个任务要等待`n`个事件发生后才能执行，则应定义为`Semp = OSSemCreate(n)`，然后在这`n`个任务分别运行时调用`OSSemPost(Semp)`，直到这`n`个事件均发生后，这个任务才能运行。
&emsp;&emsp;`OSSemCreate(cnt)`赋初始值`cnt`，`OSSemPend`执行一次，`cnt`减`1`一次；`OSSemPost`执行一次，`cnt`加`1`一次。
&emsp;&emsp;例子`1`如下(`OSSemCreate ( 0 );`)：

``` c
OS_EVENT *Fun_Semp;
Fun_Semp = OSSemCreate ( 0 );
​
void  MyTask ( void *pdata ) {
    for ( ;; ) {
        OSSemPend ( Fun_Semp, 0, &err ); /* 请求信号量 */
        PC_DispStr ( 0, ++y, s1, DISP_BGND_BLACK + DISP_FGND_WHITE );
        OSTimeDlyHMSM ( 0, 0, 1, 0 ); /* 等待1秒 */
    }
}
​
void  YouTask ( void *pdata ) {
    for ( ;; ) {
        PC_DispStr ( 0, ++y, s2, DISP_BGND_BLACK + DISP_FGND_WHITE );
​
        if ( YouTaskRun == 5 ) {
            OSSemPost ( Fun_Semp ); /* 发送信号量 */
        }
​
        YouTaskRun++;
        OSTimeDlyHMSM ( 0, 0, 2, 0 ); /* 等待2秒 */
    }
}
```

在上例中，`MyTask`一直在等待信号量，在信号量没有到来之前无法执行。只有在`YouTask`运行了`5`次，`YouTaskRun`等于`5`之后，`OSSemPost(Fun_Semp)`发送信号量，`MyTask`才得以执行。如果按上例所示，`MyTask`只能执行一次，因为`YouTask`以后再也不可能使得`YouTaskRun`等于`5`了。`MyTask`也就因为无法得到信号量而不能运行。
&emsp;&emsp;例子`2`如下(`OSSemCreate ( 1 );`)：

``` c
OS_EVENT *Fun_Semp;
Fun_Semp = OSSemCreate ( 1 );
​
void  MyTask ( void *pdata ) {
    for ( ;; ) {
        OSSemPend ( Fun_Semp, 0, &err ); /* 请求信号量 */
        PC_DispStr ( 0, ++y, s1, DISP_BGND_BLACK + DISP_FGND_WHITE );
        OSSemPost ( Fun_Semp ); /* 发送信号量 */
        OSTimeDlyHMSM ( 0, 0, 1, 0 ); /* 等待1秒 */
    }
}
​
void  YouTask ( void *pdata ) {
    for ( ;; ) {
        OSSemPend ( Fun_Semp, 0, &err ); /* 请求信号量 */
        PC_DispStr ( 0, ++y, s2, DISP_BGND_BLACK + DISP_FGND_WHITE );
        OSSemPost ( Fun_Semp ); /* 发送信号量 */
        OSTimeDlyHMSM ( 0, 0, 2, 0 ); /* 等待2秒 */
    }
}
```

在上例中，`MyTask`、`YouTask`都在等待信号量，由于`MyTask`优先级高，首先得到信号量开始执行，此时`YouTask`还在等待信号量。`MyTask`执行完毕，`OSSemPost(Fun_Semp)`发送信号量。`YouTask`得到信号量运行后发送信号量，如此反复。