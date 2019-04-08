---
title: uip之protothreads
date: 2019-02-05 11:17:27
tags:
---
&emsp;&emsp;通常我们等待一个事件时有阻塞和非阻塞两种方式，`uip`不支持多线程操作，也不依靠中断来通知事件，所以要使用阻塞的方式。但阻塞这种方式又会白白浪费`cpu`时间，阻塞在那里等待事件发生，因而`uip`使用了一种`protothreads`方式，暂称其为`协程`。
&emsp;&emsp;下面是官方文档的一些简介：协程是一种无堆栈的轻量级线程，它被设计用在服务一些简单有限的内存体系上，如一些嵌入式系统等。协程为事件驱动的线性代码执行，提供`C`的实现。协程可以被用在有或无`RTOS`(`实时操作系统`)的结构上。协程是一个非常轻量级、无堆栈线程，提供了事件驱动系统顶层的阻塞上下文的功能，而不需要每个线程的堆栈。协程的目的是实现连续流程的控制，而不需要状态机或者完整的多线程机制支持。在`C`函数中协程提供条件阻塞。
&emsp;&emsp;我们从`uip`的一个应用实例`dhcpc`中看一下协程是如何使用及其原理，在`dhcpc`初始化函数中调用了`PT_INIT(&s.pt);`。下面是`dhcpc`的应用主函数：

``` cpp
static PT_THREAD ( handle_dhcp ( void ) ) {
    PT_BEGIN ( &s.pt );
​
    do {
        send_discover(); /* 发送dhcpc探求包 */
        timer_set ( &s.timer, s.ticks );
        PT_WAIT_UNTIL ( &s.pt, uip_newdata() || timer_expired ( &s.timer ) );
​
        /* do something */
        if ( s.ticks < CLOCK_SECOND * 60 ) {
            s.ticks *= 2;
        }
    } while ( s.state != STATE_OFFER_RECEIVED );
​
    do {
        send_request(); /* 发送dhcpc接受包 */
        timer_set ( &s.timer, s.ticks );
        PT_WAIT_UNTIL ( &s.pt, uip_newdata() || timer_expired ( &s.timer ) );
​
        if ( s.ticks <= CLOCK_SECOND * 10 ) {
            s.ticks += CLOCK_SECOND;
        } else {
            PT_RESTART ( &s.pt );
        }
    } while ( s.state != STATE_CONFIG_RECEIVED );
​
    while ( 1 ) {
        PT_YIELD ( &s.pt );
    }
​
    PT_END ( &s.pt );
}
```

&emsp;&emsp;我们分别看下这几个宏的实现：

- PT_INIT：初始化协程宏，必须在执行相应的协程函数前执行此宏：

``` cpp
#define PT_INIT(pt) LC_INIT((pt)->lc)
```

我们看到这里又有一个新的宏`LC_INIT`(这是`Local continuations`部分)，其实就是初始化状态：

``` cpp
#define LC_INIT(s) s = 0;
```

下面是结构pt的定义：

``` cpp
struct pt {
    lc_t lc;
};
```

所以`PT_INIT(&s.pt);`这个宏展开就是：

``` cpp
s.pt->lc = 0;
```

- `PT_THREAD`：定义协程函数的宏，凡是要使用协程的函数都要被此宏定义：

``` cpp
#define PT_THREAD(name_args) char name_args
```

- `PT_BEGIN`：协程开始宏：

``` cpp
#define PT_BEGIN(pt) { char PT_YIELD_FLAG = 1; LC_RESUME((pt)->lc)
```

其中`LC_RESUME`宏：

``` cpp
#define LC_RESUME(s) switch(s) { case 0:
```

所以`PT_BEGIN(&s.pt);`宏展开如下：

``` cpp
char PT_YIELD_FLAG = 1;
​
switch ( s.pt->lc ) {
case 0:
```

可以看出这个宏就是定义了一个`switch`的头。

- `PT_WAIT_UNTIL`：等待条件成立宏，此宏将`阻塞`在这个条件下：

``` cpp
#define PT_WAIT_UNTIL(pt, condition) \
    do {                             \
        LC_SET((pt)->lc);            \
        if(!(condition)) {           \
            return PT_WAITING;       \
        }                            \
    } while(0)
```

其中`LC_SET`宏为：

``` cpp
#define LC_SET(s) s = __LINE__; case __LINE__:
```

`__LINE__`是编译器内部产生的变量，它表示当前程序的行数。所以将宏展开为：

``` cpp
do {
    s = __LINE__; case __LINE__;
​
    if ( ! ( condition ) ) {
        return PT_WAITING;
    }
} while ( 0 )
```

- `PT_RESTART`：重启协程宏：

``` cpp
#define PT_RESTART(pt)     \
    do {                   \
        PT_INIT(pt);       \ /* 关键，将条件归0 */
        return PT_WAITING; \
    } while(0)
```

- `PT_YIELD`：放弃执行宏，此宏功能是放弃此次执行函数返回：

``` cpp
#define PT_YIELD(pt)             \
    do {                         \
        PT_YIELD_FLAG = 0;       \
        LC_SET((pt)->lc);        \
        if(PT_YIELD_FLAG == 0) { \
            return PT_YIELDED;   \
        }                        \
    } while(0)
```

- `PT_END`：协程结束宏：

``` cpp
#define PT_END(pt) LC_END((pt)->lc); PT_YIELD_FLAG = 0; \
    PT_INIT(pt); return PT_ENDED; }
```

其中`LC_END`宏定义为：

``` cpp
#define LC_END(s) }
```

&emsp;&emsp;我们将上面那个函数展开：

``` cpp
static char handle_dhcp ( void ) {
    char PT_YIELD_FLAG = 1;
​
    switch ( s.pt->lc ) {
        case 0:
            do {
                send_discover(); /* 发送dhcpc探求包 */
                timer_set ( &s.timer, s.ticks );
​
                do {
                    s.pt->lc = __LINE__; case __LINE__;
​
                    if ( ! ( uip_newdata() ) ) {
                        return PT_WAITING;
                    }
                } while ( 0 );
            } while ( s.state != STATE_OFFER_RECEIVED );
​
            do {
                send_request(); /* 发送dhcpc接受包 */
                timer_set ( &s.timer, s.ticks );
​
                do {
                    s.pt->lc = __LINE__; case __LINE__;
​
                    if ( ! ( uip_newdata() ) ) {
                        return PT_WAITING;
                    }
                } while ( 0 );
​
                if ( s.ticks <= CLOCK_SECOND * 10 ) {
                    s.ticks += CLOCK_SECOND;
                } else {
                    do {
                        s.pt->lc = 0;
                        return PT_WAITING;
                    } while ( 0 );
                }
            } while ( s.state != STATE_CONFIG_RECEIVED );
​
            while ( 1 ) { /* 这个死循环是应用中的需求，dhcp后这个程序不要再执行了 */
                do {
                    PT_YIELD_FLAG = 0;
                    s.pt->lc = __LINE__; case __LINE__:

                    if ( PT_YIELD_FLAG == 0 ) {
                        return PT_YIELDED;
                    }
                } while ( 0 );
            }
    }
​
    PT_YIELD_FLAG = 0;
    s.pt->lc = 0;
    return PT_ENDED;
}
```

---

&emsp;&emsp;`protothread`是专为资源有限的系统设计的一种耗费资源特别少并且不使用堆栈的线程模型，相比于嵌入式操作系统，其有如下优点：

- 以纯`C`语言实现，无硬件依靠性，因此不存在移植的困难。
- 极少的资源需求，每个`Protothread`仅需要`2`个额外的字节。
- 支持阻塞操纵且没有栈的切换。

&emsp;&emsp;它的缺陷在于：

- 函数中不具备可重入型，不能使用局部变量。
- 按顺序判断各任务条件是否满足，因此无优先级抢占。
- 任务中的各条件也是按顺序判断的，因此要求任务中的条件必须是依次出现的。

&emsp;&emsp;`protothread`的阻塞机制：在每个条件判断前，先将当前地址保存到某个变量中，再判断条件是否成立，若条件成立，则往下运行；若条件不成立，则返回。`protothread`基本源码及注释：

``` cpp
#ifndef PC_H
#define PC_H
​
typedef unsigned int INT16U;
​
struct pt {
    INT16U lc;
};
​
#define PT_THREAD_WAITING 0
#define PT_THREAD_EXITED  1
​
#define PT_INIT(pt) (pt)->lc = 0 /* 初始化任务变量，只在初始化函数中执行一次就行 */
#define PT_BEGIN(pt) switch((pt)->lc) { case 0: /* 启动任务处理，放在函数开始处 */
​
/* 等待某个条件成立，若条件不成立则直接退出本函数，下一次进入本函数就直接跳到这个地方判断。
   “__LINE__”是编译器内置宏，代表当前行号，比如：若当前行号为8，
   则“s = __LINE__; case __LINE__:”展开为“s = 8; case 8:” */
#define PT_WAIT_UNTIL(pt, condition) (pt)->lc = __LINE__; case __LINE__: \
    if(!(condition))  return
​
#define PT_END(pt) } /* 结束任务，放在函数的最后 */
#define PT_WAIT_WHILE(pt, cond) PT_WAIT_UNTIL((pt), !(cond)) /* 等待某个条件不成立 */
#define PT_WAIT_THREAD(pt, thread) PT_WAIT_UNTIL((pt), (thread)) /* 等待某个子任务执行完成 */
#define PT_SPAWN(pt,thread) \ /* 新建一个子任务，并等待其执行完退出 */
    PT_INIT ( ( pt ) ); \
    PT_WAIT_THREAD ( ( pt ), ( thread ) )
​
#define PT_RESTART(pt) PT_INIT(pt); return /* 重新启动某任务执行 */
#define PT_EXIT(pt)    (pt)->lc = PT_THREAD_EXITED; return /* 任务后面的部分不执行，直接退出 */
​
#endif
```

&emsp;&emsp;应用示例如下：

``` cpp
static struct pt pt1, pt2;
static int protothread1_flag, protothread2_flag;
​
static void protothread1 ( struct pt *pt ) { /* 线程1 */
    PT_BEGIN ( pt ); /* 开始时调用 */
​
    while ( 1 ) {
        protothread1_flag = 1;
        /* 等待protothread2_flag标志置位 */
        PT_WAIT_UNTIL ( pt, protothread2_flag != 0 );
        protothread2_flag = 0;
    }
​
    PT_END ( pt ); /* 结束时调用 */
}
​
static void protothread2 ( struct pt *pt ) { /* 线程2 */
    PT_BEGIN ( pt );
​
    while ( 1 ) {
        protothread2_flag = 1;
        PT_WAIT_UNTIL ( pt, protothread1_flag != 0 );
        protothread1_flag = 0;
    }
​
    PT_END ( pt );
}
​
void main ( void ) {
    PT_INIT ( &pt1 ); /* 初始化 */
    PT_INIT ( &pt2 );
​
    while ( 1 ) {
        protothread1 ( &pt1 );
        protothread2 ( &pt2 );
    }
}
```

线程`1`和线程`2的展开式如下：

``` cpp
static void protothread1 ( struct pt *pt ) { /* 线程1 */
    switch ( pt->lc ) {
        case 0:
            ;
​
            while ( 1 ) {
                protothread1_flag = 1;
                pt->lc = 26; case 26: /* 假定当前为26行 */

                if ( protothread2_flag == 0 ) {
                    return; /* 若protothread2_flag未发生，则返回 */
                }
​
                protothread2_flag = 0;
            }
    }
}
​
static void protothread2 ( struct pt *pt ) {
    switch ( pt->lc ) {
        case 0:
            ;
​
            while ( 1 ) {
                protothread2_flag = 1;
                pt->lc = 44; case 44:

                if ( protothread1_flag == 0 ) {
                    return;
                }
​
                myFunc2();
                protothread1_flag = 0;
            }
    }
}
```