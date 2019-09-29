---
title: ucos互斥信号量
categories: ucos和ucgui
abbrlink: d069346e
date: 2018-12-29 13:42:29
---
&emsp;&emsp;在`ucos`信号量使用过程中，经常会用到二值信号量。而在二值信号量中，最常用的情况就是互斥信号量。互斥信号是本身是一种二进制信号，具有超出`ucos`提供的一般信号机制的特性。由于其特殊性，`ucos`的作者组织了一套关于互斥信号量管理的函数。互斥信号量具有以下特点：解决优先级反转问题、实现对资源的独占式访问(二值信号量)。
&emsp;&emsp;在应用程序中，使用互斥信号是为了减少优先级翻转问题。当一个高优先级的任务需要的资源被一个低优先级的任务使用时，就会发生优先级翻转。为了解决该问题，内核可以提升低优先级任务的优先级，先于高优先级的任务运行，释放占用的资源。
&emsp;&emsp;例如有三个任务可以使用共同的资源，为了访问这个资源，每个任务必须在互斥信号`ResourceMutex上`等待(`pend`)。任务`1`有最高优先级`10`，任务`2`优先级为`15`，任务`3`优先级为`20`。一个没有使用的正好在最高优先级之上的优先级`9`用来作为优先级继承优先级。如main所示，代码`(1)`中进行`ucos`初始化，并通过调用`OSMutexCreate`在代码`(2)`中创建了一个互斥信号。需要注意的是，`OSMutexCreate`函数使用`PIP`作为参数。然后在代码`(3)`中创建三个任务，在代码`(4)`中启动`ucos`。
&emsp;&emsp;假设任务运行了一段时间，在某个时间点，任务`3`最先访问了共同的资源，并得到了互斥信号。任务`3`运行了一段时间后被任务`1`抢占，任务`1`需要使用这个资源，并通过调用`OSMutexPend`企图获得互斥信号。这种情况下，`OSMutexPend`会发现一个高优先级的任务需要这个资源，就会把任务`3`的优先级提高到`9`，同时强迫进行上下文切换退回到任务`3`执行。任务`3`可以继续执行，然后释放占用的共同资源。任务`3`通过调用`OSMutexPost`释放占用的`mutex`信号，`OSMutexPost`会发现`mutex`被一个优先级提升的低优先级任务占有，就会把任务`3`的优先级返回到`20`。然后把资源释放给任务`1`使用，执行上下文切换到任务`1`。

``` c
OS_EVENT *ResourceMutex;
OS_STK TaskPrio10Stk[1000];
OS_STK TaskPrio15Stk[1000];
OS_STK TaskPrio20Stk[1000];
​
void main ( void ) {
    INT8U err;
    OSInit(); /*(1)*/
    /* 应用程序初始化 */
    ResourceMutex = OSMutexCreate ( 9, &err ); /* (2) */
    OSTaskCreate ( TaskPrio10, ( void * ) 0, &TaskPrio10Stk[999], 10 ); /* (3) */
    OSTaskCreate ( TaskPrio15, ( void * ) 0, &TaskPrio15Stk[999], 15 );
    OSTaskCreate ( TaskPrio20, ( void * ) 0, &TaskPrio20Stk[999], 20 );
    /* Application Initialization */
    OSStart(); /* (4) */
}
​
void TaskPrio10 ( void *pdata ) {
    INT8U err;
    pdata = pdata;
​
    while ( 1 ) {
        /* 应用程序代码 */
        OSMutexPend ( ResourceMutex, 0, &err );
        /* 访问共享资源 */
        OSMutexPost ( ResourceMutex );
        /* 应用程序代码 */
    }
}
​
void TaskPrio15 ( void *pdata ) {
    INT8U err;
    pdata = pdata;
​
    while ( 1 ) {
        /* 应用程序代码 */
        OSMutexPend ( ResourceMutex, 0, &err );
        /* 访问共享资源 */
        OSMutexPost ( ResourceMutex );
        /* 应用程序代码 */
    }
}
​
void TaskPrio20 ( void *pdata ) {
    INT8U err;
    pdata = pdata;
​
    while ( 1 ) {
        /* 应用程序代码 */
        OSMutexPend ( ResourceMutex, 0, &err );
        /* 访问共享资源 */
        OSMutexPost ( ResourceMutex );
        /* 应用程序代码 */
    }
}
```

&emsp;&emsp;`ucos`互斥信号包含三个元素，`flag`表示当前`mutex`是否能够获得(`0`或`1`)；`priority`表示使用这个`mutex`的任务，以防止一个高优先级的任务需要访问`mutex`；还包括一个等待这个`mutex`的任务列表。为了启动`ucos`的`mutex`服务，应该在`OS_CFG.H`中设置`OS_MUTEX_EN`为`1`。在使用一个互斥信号之前应该创建它，创建一个`mutex`信号通过调用`OSMutexCreate`完成，`mutex`的初始值总是设置为`1`，表示资源可以获得。
&emsp;&emsp;`ucos`提供了六种访问互斥信号量的操作：`OSMutexCreate`、`OSMutexDel`、`OSMutexPend`、`OSMutexPost`、`OSMutexAccept`和`OSMutexQuery`，这些函数展示了任务和互斥信号量的关系。