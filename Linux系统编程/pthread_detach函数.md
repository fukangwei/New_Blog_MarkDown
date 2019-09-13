---
title: pthread_detach函数
date: 2018-12-29 16:59:37
categories: Linux系统编程
---
&emsp;&emsp;`linux`线程执行和`windows`不同，`pthread`有两种状态：`joinable`状态和`unjoinable`状态。如果线程是`joinable`状态，当线程函数自己返回退出时或`pthread_exit`时都不会释放线程所占用堆栈和线程描述符(总计`8K`多)。只有当你调用了`pthread_join`之后，这些资源才会被释放。若是`unjoinable`状态的线程，这些资源在线程函数退出或`pthread_exit`时自动会被释放。
&emsp;&emsp;`unjoinable`属性可以在`pthread_create`时指定，或在线程创建后在线程中`pthread_detach`自己。例如`pthread_detach(pthread_self())`，将状态改为`unjoinable`状态，确保资源的释放。或者将线程置为`joinable`，然后适时调用`pthread_join`函数。
&emsp;&emsp;其实简单的说就是在线程函数头加上`pthread_detach(pthread_self())`的话，线程状态就会改变，在函数尾部直接`pthread_exit`，线程就会自动退出，并释放自己的资源。

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>
​
void print_message_function ( void *ptr );
​
int main ( void ) {
    pthread_t thread1;
​
    while ( 1 ) {
        pthread_create (
            &thread1, NULL,
            ( void * ) &print_message_function, ( void * ) 0 );
        printf ( "%ld\n", thread1 );
        sleep ( 1 );
    }
​
    return ( 0 ) ;
}
​
void print_message_function ( void *ptr ) {
    pthread_detach ( pthread_self() );
    static int g;
    printf ( "%d\n", g++ );
    pthread_exit ( 0 ) ;
}
```

---

### pthread_detach作用

&emsp;&emsp;创建一个线程默认的状态是`joinable`，如果一个线程结束运行但没有被`join`，则它的状态类似于进程中的`Zombie Process`，即还有一部分资源没有被回收(退出状态码)，所以创建线程者应该调用`pthread_join`来等待线程运行结束，并可得到线程的退出代码，回收其资源(类似于`wait`、`waitpid`)。
&emsp;&emsp;但是调用`pthread_join(pthread_id)`后，如果该线程没有运行结束，调用者会被阻塞。在有些情况下我们并不希望如此，比如在`Web`服务器中，当主线程为每个新来的链接创建一个子线程进行处理的时候，主线程并不希望因为调用`pthread_join`而阻塞(因为还要继续处理之后到来的链接)，这时可以在子线程中加入代码`pthread_detach(pthread_self())`或者父线程调用`pthread_detach(thread_id)`(非阻塞，可立即返回)，将该子线程的状态设置为`detached`，则该线程运行结束后会自动释放所有资源。