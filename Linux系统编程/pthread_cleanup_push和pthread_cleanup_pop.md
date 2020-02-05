---
title: pthread_cleanup_push和pthread_cleanup_pop
categories: Linux系统编程
abbrlink: 2194d977
date: 2019-02-02 20:41:38
---
&emsp;&emsp;一般来说，`Posix`的线程终止有两种情况：正常终止和非正常终止。线程主动调用`pthread_exit`或者从线程函数中`return`都将使线程正常退出，这是可预见的退出方式；非正常终止是线程在其他线程的干预下，或者由于自身运行出错(比如访问非法地址)而退出，这种退出方式是不可预见的。不论是可预见的线程终止还是异常终止，都会存在资源释放的问题，在不考虑因运行出错而退出的前提下，如何保证线程终止时能顺利的释放掉自己所占用的资源，特别是锁资源，就是一个必须考虑解决的问题。<!--more-->
&emsp;&emsp;最经常出现的情形是资源独占锁的使用：线程为了访问临界资源而为其加上锁，但在访问过程中被外界取消，如果线程处于响应取消状态，且采用异步方式响应，或者在打开独占锁以前的运行路径上存在取消点，则该临界资源将永远处于锁定状态得不到释放。外界取消操作是不可预见的，因此的确需要一个机制来简化用于资源释放的编程。
&emsp;&emsp;在`POSIX`线程`API`中提供了一个`pthread_cleanup_push/pthread_cleanup_pop`函数对用于自动释放资源：从`pthread_cleanup_push`的调用点到`pthread_cleanup_pop`之间的程序段中的终止动作(包括调用`pthread_exit`和取消点终止)都将执行`pthread_cleanup_push`所指定的清理函数。`API`定义如下：

``` cpp
void pthread_cleanup_push ( void ( *routine ) ( void * ), void *arg );
void pthread_cleanup_pop ( int execute );
```

&emsp;&emsp;`pthread_cleanup_push/pthread_cleanup_pop`采用先入后出的栈结构管理，`void routine(void *arg)`函数在调用`pthread_cleanup_push`时压入清理函数栈，多次对`pthread_cleanup_push`的调用将在清理函数栈中形成一个函数链，在执行该函数链时按照压栈的相反顺序弹出。`execute`参数表示执行到`pthread_cleanup_pop`时是否在弹出清理函数的同时执行该函数，为`0`表示不执行，非`0`为执行；这个参数并不影响异常终止时清理函数的执行。
&emsp;&emsp;`pthread_cleanup_push/pthread_cleanup_pop`是以宏方式实现的，这是`pthread.h`中的宏定义：

``` cpp
#define pthread_cleanup_push(routine, arg)                        \
    {                                                             \
        struct _pthread_cleanup_buffer _buffer;                   \
        _pthread_cleanup_push ( &_buffer, ( routine ), ( arg ) );

#define pthread_cleanup_pop(execute)                \
    _pthread_cleanup_pop ( &_buffer, ( execute ) ); \
    }
```

可见，`pthread_cleanup_push`带有一个`{`，而`pthread_cleanup_pop`带有一个`}`，因此这两个函数必须成对出现，且必须位于程序的同一级别的代码段中才能通过编译。在下面的例子里，当线程在`do some work`中终止时，将主动调用`pthread_mutex_unlock(mut)`，以完成解锁动作。

``` cpp
pthread_cleanup_push ( pthread_mutex_unlock, ( void * ) &mut );
pthread_mutex_lock ( &mut );
/* do some work */
pthread_mutex_unlock ( &mut );
pthread_cleanup_pop ( 0 );
```

必须要注意的是，如果线程处于`PTHREAD_CANCEL_ASYNCHRONOUS`状态，上述代码段就有可能出错，因为`CANCEL`事件有可能在`pthread_cleanup_push`和`pthread_mutex_lock`之间发生，或者在`pthread_mutex_unlock`和`pthread_cleanup_pop`之间发生，从而导致清理函数`unlock`一个并没有加锁的`mutex`变量，造成错误。因此在使用清理函数的时候，都应该暂时设置成`PTHREAD_CANCEL_DEFERRED`模式。为此，`POSIX`的`Linux`实现中还提供了一对不保证可移植的`pthread_cleanup_push_defer_np/pthread_cleanup_pop_defer_np`扩展函数，功能与以下代码段相当：

``` cpp
int oldtype;
pthread_setcanceltype ( PTHREAD_CANCEL_DEFERRED, &oldtype );
pthread_cleanup_push ( routine, arg );
...
pthread_cleanup_pop ( execute );
pthread_setcanceltype ( oldtype, NULL );
```

&emsp;&emsp;`pthread_cleanup_push`指定的清理的函数调用的时机只有下述三种情况：

- 调用`pthread_exit`函数。
- 响应取消请求时(包括其他异常退出)。
- 调用参数非`0`的`pthread_cleanup_pop`函数。

&emsp;&emsp;**补充说明**：在线程宿主函数中主动调用`return`，如果`return`语句包含在`pthread_cleanup_push/pthread_cleanup_pop`对中，则不会引起清理函数的执行，反而会导致`segment fault`。