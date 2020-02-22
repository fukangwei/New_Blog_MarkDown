---
title: Qt之QThread
categories: Qt语法详解
date: 2019-01-23 17:16:21
---
### 简述

&emsp;&emsp;`QThread`类提供了与系统无关的线程，其代表在程序中一个单独的线程控制。线程在`run`中开始执行，默认情况下，`run`通过调用`exec`启动事件循环并在线程里运行一个`Qt`的事件循环。<!--more-->
&emsp;&emsp;当线程`started`和`finished`时，`QThread`会通过一个信号通知你，可以使用`isFinished`和`isRunning`来查询线程的状态。你可以通过调用`exit`或`quit`来停止线程。在极端情况下，可能要强行`terminate`一个执行线程，但是这样做很危险。从`Qt 4.8`起，可以释放运行刚刚结束的线程对象，通过连接`finished`信号到`QObject::deleteLater`槽。使用`wait`来阻塞调用的线程，直到其它线程执行完毕(或者直到指定的时间过去)。
&emsp;&emsp;`QThread`还提供了静态的、平台独立的休眠函数：`sleep`、`msleep`、`usleep`，允许秒、毫秒和微秒来区分，这些函数在`Qt 5.0`中被设为`public`。一般情况下不需要`wait`和`sleep`函数，因为`Qt`是一个事件驱动型框架。考虑监听`finished`信号来取代`wait`，使用`QTimer`来取代`sleep`。
&emsp;&emsp;静态函数`currentThreadId`和`currentThread`返回标识当前正在执行的线程。前者返回该线程平台特定的`ID`，后者返回一个线程指针。
&emsp;&emsp;要设置线程的名称，可以在启动线程之前调用`setObjectName`。如果不调用`setObjectName`，线程的名称将是线程对象的运行时类型(`QThread`子类的类名)。

### 线程启动

&emsp;&emsp;`start`函数原型如下：

``` cpp
void start ( Priority priority = InheritPriority ) [slot]
```

调用后会执行`run`函数，但在`run`函数执行前会发射信号`started`，操作系统将根据优先级参数调度线程。如果线程已经在运行，那么这个函数什么也不做。优先级参数的效果取决于操作系统的调度策略，那些不支持线程优先级的系统优先级将会被忽略。

### 线程执行

&emsp;&emsp;`exec`函数原型如下：

``` cpp
int exec() [protected]
```

进入事件循环并等待直到调用`exit`，返回值是通过调用`exit`来获得，如果调用成功则返回`0`。
&emsp;&emsp;`run`函数原型如下：

``` cpp
void run() [virtual protected]
```

这是线程的起点，在调用`start`之后，新创建的线程就会调用这个函数。大多数情况下需要重新实现这个函数，便于管理自己的线程。该方法返回时，该线程的执行将结束。

### 线程退出

&emsp;&emsp;`quit`函数原型如下：

``` cpp
void quit() [slot]
```

告诉线程事件循环退出，返回`0`表示成功，相当于调用了`QThread::exit(0)`。
&emsp;&emsp;`exit`函数原型如下：

``` cpp
void exit ( int returnCode = 0 )
```

告诉线程事件循环退出。调用这个函数后，线程离开事件循环后返回，`QEventLoop::exec`返回`returnCode`。按照惯例，`0`表示成功，任何非`0`值表示失败。
&emsp;&emsp;`terminate`函数原型如下：

``` cpp
void terminate() [slot]
```

终止线程，线程可能会立即被终止也可能不会，这取决于操作系统的调度策略。使用`terminate`之后再使用`QThread::wait`，以确保万无一失。当线程被终止后，所有等待中的线程将会被唤醒。此函数比较危险，不鼓励使用。线程可以在代码执行的任何点被终止，尤其可能在更新数据时被终止，从而没有机会来清理资源或者解锁等。总之，只有在绝对必要时才使用此函数。
&emsp;&emsp;`requestInterruption`函数原型如下：

``` cpp
void requestInterruption()
```

请求线程的中断。此函数不停止线程上运行的任何事件循环，并且在任何情况下都不会终止它。

### 线程等待

&emsp;&emsp;函数原型如下：

``` cpp
/* 强制当前线程睡眠msecs毫秒 */
void msleep ( unsigned long msecs ) [static]
/* 强制当前线程睡眠secs秒 */
void sleep ( unsigned long secs ) [static]
/* 强制当前线程睡眠usecs微秒 */
void usleep ( unsigned long usecs ) [static]
/* 线程将会被阻塞，等待time毫秒。和sleep不同的是，如果线程退出，wait会返回 */
bool wait ( unsigned long time = ULONG_MAX )
```

### 线程状态

&emsp;&emsp;`isFinished`和`isRunning`函数原型如下：

``` cpp
bool isFinished() const /* 线程是否结束 */
bool isRunning() const /* 线程是否正在运行 */
```

&emsp;&emsp;`isInterruptionRequested`函数原型如下：

``` cpp
bool isInterruptionRequested() const
```

如果线程上的任务运行应该停止，则返回`true`。可以使用`requestInterruption`请求中断。此函数可用于使长时间运行的任务干净地中断。注意，不要过于频繁调用，以保持较低的开销。

``` cpp
void long_task() {
    forever {
        if ( QThread::currentThread()->isInterruptionRequested() ) {
            return;
        }
    }
}
```

### 线程优先级

&emsp;&emsp;`setPriority`函数原型如下：

``` cpp
void setPriority(Priority priority)
```

设置正在运行线程的优先级。如果线程没有运行，此函数不执行任何操作并立即返回。使用的`start`来启动一个线程具有特定的优先级。优先级参数可以是`QThread::Priority`枚举除了`InheritPriortyd`的任何值。枚举量`QThread::Priority`如下：

常量                            | 值  | 描述
--------------------------------|-----|-----------
`QThread::IdlePriority`         | `0` | 没有其它线程运行时才调度
`QThread::LowestPriority`       | `1` | 比`LowPriority`调度频率低
`QThread::LowPriority`          | `2` | 比`NormalPriority`调度频率低
`QThread::NormalPriority`       | `3` | 操作系统的默认优先级
`QThread::HighPriority`         | `4` | 比`NormalPriority`调度频繁
`QThread::HighestPriority`      | `5` | 比`HighPriority`调度频繁
`QThread::TimeCriticalPriority` | `6` | 尽可能频繁的调度
`QThread::InheritPriority`      | `7` | 使用和创建线程同样的优先级，这是默认值