---
title: Linux多线程编程
date: 2019-02-03 18:42:31
tags:
---
### Linux进程与线程

&emsp;&emsp;`Linux`进程创建一个新线程时，线程将拥有自己的栈(因为线程有自己的局部变量)，但与它的创建者共享全局变量、文件描述符、信号句柄和当前目录状态。`Linux`通过`fork`创建子进程与创建线程之间是有区别的：`fork`创建出该进程的一份拷贝，这个新进程拥有自己的变量和自己的`PID`，它的时间调度是独立的，它的执行几乎完全独立于父进程。进程可以看成一个资源的基本单位，而线程是程序调度的基本单位，一个进程内部的线程之间共享进程获得的时间片。

### \_REENTRANT宏

&emsp;&emsp;在一个多线程程序里，默认情况下，只有一个`errno`变量供所有的线程共享。在一个线程准备获取刚才的错误代码时，该变量很容易被另一个线程中的函数调用所改变。类似的问题还存在于`fputs`之类的函数中，这些函数通常用一个单独的全局性区域来缓存输出数据。
&emsp;&emsp;为解决这个问题，需要使用可重入的例程。可重入代码可以被多次调用而仍然工作正常。编写的多线程程序，通过定义宏`_REENTRANT`来告诉编译器我们需要可重入功能，这个宏的定义必须出现于程序中的任何`#include`语句之前。`_REENTRANT`为我们做三件事情，并且做的非常优雅：

- 它会对部分函数重新定义它们的可安全重入的版本，这些函数名字一般不会发生改变，只是会在函数名后面添加`_r`字符串，例如将函数`gethostbyname`变成`gethostbyname_r`。
- `stdio.h`中原来以宏的形式实现的一些函数将变成可安全重入函数。
- 在`error.h`中定义的变量`error`现在将成为一个函数调用，它能够以一种安全的多线程方式来获取真正的`errno`的值。

### 线程的基本函数

&emsp;&emsp;大多数`pthread_XXX`系列的函数在失败时，并未遵循`UNIX`函数的惯例返回`-1`，这种情况在`UNIX`函数中属于一少部分。如果调用成功，则返回值是`0`，如果失败则返回错误代码。

#### 线程创建

&emsp;&emsp;`pthread_create`函数原型如下：

``` cpp
#include <pthread.h>
int pthread_create ( pthread_t *thread, pthread_attr_t *attr, void * ( *start_routine ) ( void * ), void *arg );
```

- `thread`：指向`pthread_create`类型的指针，用于引用新创建的线程。
- `attr`：用于设置线程的属性，一般不需要特殊的属性，所以可以简单地设置为`NULL`。
- `(* start_routine)(void *)`：传递新线程所要执行的函数地址。
- `arg`：新线程所要执行的函数的参数。

如果调用成功，则返回值是`0`；如果失败，则返回错误代码。`pthread_attr_t`的结构体如下：

``` cpp
typedef struct {
    int detachstate; /* 线程的分离状态 */
    int schedpolicy; /* 线程调度策略 */
    struct sched_param schedparam; /* 线程的调度参数 */
    int inheritsched; /* 线程的继承性 */
    int scope; /* 线程的作用域 */
    size_t guardsize; /* 线程栈末尾的警戒缓冲区大小 */
    int stackaddr_set;
    void *stackaddr; /* 线程栈的位置 */
    size_t stacksize; /* 线程栈的大小 */
} pthread_attr_t;
```

#### 线程终止

&emsp;&emsp;`pthread_exit`函数原型如下：

``` cpp
#include <pthread.h>
void pthread_exit ( void *retval );
```

参数`retval`指向线程向要返回的某个对象。线程通过调用`pthread_exit`函数终止执行，并返回一个指向某对象的指针。注意，绝不能用它返回一个指向局部变量的指针，因为线程调用该函数后，这个局部变量就不存在了，这将引起严重的程序漏洞。

#### 线程同步

&emsp;&emsp;`pthread_join`函数原型如下：

``` cpp
#include <pthread.h>
int pthread_join ( pthread_t th, void **thread_return );
```

- `th`：将要等待结束的线程，线程通过`pthread_create`返回的标识符来指定。
- `thread_return`：一个指针，指向另一个指针，而后者指向线程的返回值。

调用成功完成后，`pthrea_join`返回`0`，其他任何返回值都表示出现了错误。如果检测到以下任意情况，`pthread_join`将失败并返回相应的值：

- `ESRCH`：没有找到与给定的线程`ID`相对应的线程。如果多个线程等待同一个线程终止，则所有等待线程将一直等到目标线程终止。然后一个等待线程成功返回，其余的等待线程将失败，返回`ESRCH`错误。
- `EDEADLK`：将出现死锁，如一个线程等待其本身，或者线程`A`和线程`B`互相等待。
- `EINVAL`：与给定的线程`ID`相对应的线程是分离线程。

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void *thread_function ( void *arg );
char message[] = "Hello World";

int main() {
    int res;
    pthread_t a_thread;
    void *thread_result;
    res = pthread_create ( &a_thread, NULL, thread_function, ( void * ) message );

    if ( res != 0 ) {
        perror ( "Thread creation failed!" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Waiting for thread to finish...\n" );
    res = pthread_join ( a_thread, &thread_result );

    if ( res != 0 ) {
        perror ( "Thread join failed!\n" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Thread joined, it returned %s\n", ( char * ) thread_result );
    printf ( "Message is now %s\n", message );
    exit ( EXIT_FAILURE );
}

void *thread_function ( void *arg ) {
    printf ( "thread_function is running. Argument was %s\n", ( char * ) arg );
    sleep ( 3 );
    strcpy ( message, "Bye!" );
    pthread_exit ( "Thank you for your CPU time!" );
}
```

编译这个程序需要定义宏`_REENTRANT`：

``` cpp
gcc -D_REENTRANT thread1.c -o thread1 –lpthread
```

执行结果：

``` cpp
Waiting for thread to finish...
thread_function is running. Argument was Hello World
Thread joined, it returned Thank you for your CPU time!
Message is now Bye!
```

&emsp;&emsp;`pthread_exit(void *retval)`本身返回的就是指向某个对象的指针，因此，`pthread_join(pthread_t th, void **thread_return);`中的`thread_return`是二级指针，指向线程返回值的指针。
&emsp;&emsp;可以看到，我们创建的新线程修改的数组`message`的值，而原先的线程也可以访问到。如果我们调用的是`fork`而不是`pthread_create`，就不会有这样的效果了。原因是`fork`创建子进程之后，子进程会拷贝父进程，两者分离，相互不干扰，而线程之间则是共享进程的相关资源。

#### 线程的同时执行

&emsp;&emsp;接下来，我们来编写一个程序，以验证两个线程的执行是同时进行的。当然，如果是在一个单处理器系统上，线程的同时执行就需要靠`CPU`在线程之间的快速切换来实现了。我们的程序需要利用一个原理：即除了局部变量外，所有其他的变量在一个进程中的所有线程之间是共享的。在这个程序中，我们是在两个线程之间使用轮询技术，这种方式称为忙等待，所以它的效率会很低。
&emsp;&emsp;下面的代码中，两个线程会不断的轮询判断`flag`的值是否满足各自的要求：

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

int flag = 1;
void *thread_function ( void *arg );

int main() {
    int res;
    pthread_t a_thread;
    void *thread_result;
    int count = 1;
    res = pthread_create ( &a_thread, NULL, thread_function, NULL );

    if ( res != 0 ) {
        perror ( "Thread creation failed" );
        exit ( EXIT_FAILURE );
    }

    while ( count++ <= 20 ) {
        if ( flag == 1 ) {
            printf ( "1" );
            flag = 2;
        } else {
            sleep ( 1 );
        }
    }

    printf ( "\nWaiting for thread to finish...\n" );
    res = pthread_join ( a_thread, &thread_result );

    if ( res != 0 ) {
        perror ( "Thread join failed" );
        exit ( EXIT_FAILURE );
    }

    exit ( EXIT_SUCCESS );
}

void *thread_function ( void *arg ) {
    int count = 1;

    while ( count++ <= 20 ) {
        if ( flag == 2 ) {
            printf ( "2" );
            flag = 1;
        } else {
            sleep ( 1 );
        }
    }
}
```

编译这个程序：

``` cpp
gcc -D_REENTRANT thread2.c -o thread2 –lpthread
```

执行结果：

``` cpp
121212121212121212
Waiting for thread to finish...
```

#### 线程的同步

&emsp;&emsp;在上述示例中，我们采用轮询的方式在两个线程之间不停地切换是非常笨拙且没有效率的实现方式。幸运的是，专门有一些设计好的函数为我们提供更好的控制线程执行和访问代码临界区的方法。
&emsp;&emsp;本小节将介绍两个线程同步的基本方法：`信号量`和`互斥量`，这两种方法很相似，事实上，它们可以互相通过对方来实现。但在实际的应用中，对于一些情况，可能使用信号量或互斥量中的一个更符合问题的语义，并且效果更好。

##### 用信号量进行同步

&emsp;&emsp;使用`sem_init`创建信号量：

``` cpp
#include <semaphore.h>
int sem_init ( sem_t *sem, int pshared, unsigned int value );
```

- `sem`：信号量对象。
- `pshared`：控制信号量的类型，`0`表示这个信号量是当前进程的局部信号量，否则这个信号量就可以在多个进程之间共享。
- `value`：信号量的初始值。

&emsp;&emsp;使用`sem_wait`和`sem_post`信号量控制：

``` cpp
#include <semaphore.h>
int sem_wait ( sem_t *sem );
int sem_post ( sem_t *sem );
```

`sem_post`的作用是以原子操作的方式给信号量的值加`1`。`sem_wait`的作用是以原子操作的方式给信号量的值减`1`，但它会等到信号量非`0`时才会开始减法操作。如果对值为`0`的信号量调用`sem_wait`，这个函数就会等待，直到有线程增加了该信号量的值使其不再为`0`。
&emsp;&emsp;使用`sem_destory`销毁信号量：

``` cpp
#include <semaphore.h>
int sem_destory ( sem_t *sem );
```

这个函数的作用是，用完信号量后对它进行清理，清理该信号量所拥有的资源。如果你试图清理的信号量正被一些线程等待，就会收到一个错误。
&emsp;&emsp;与大多数`Linux`函数一样，这些函数在成功时都返回`0`。
&emsp;&emsp;下面编码实现输入字符串，统计每行的字符个数，以`end`结束输入：

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <semaphore.h>

#define SIZE 1024

void *thread_function ( void *arg );
char buffer[SIZE];
sem_t sem;

int main() {
    int res;
    pthread_t a_thread;
    void *thread_result;
    res = sem_init ( &sem, 0, 0 );

    if ( res != 0 ) {
        perror ( "Sem init failed" );
        exit ( EXIT_FAILURE );
    }

    res = pthread_create ( &a_thread, NULL, thread_function, NULL );

    if ( res != 0 ) {
        perror ( "Thread create failed" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Input some text. Enter 'end' to finish\n" );

    while ( scanf ( "%s", buffer ) ) {
        sem_post ( &sem );
        if ( strncmp ( "end", buffer, 3 ) == 0 ) {
            break;
        }
    }

    printf ( "\nWaiting for thread to finish...\n" );
    res = pthread_join ( a_thread, &thread_result );

    if ( res != 0 ) {
        perror ( "Thread join failed" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Thread join\n" );
    sem_destroy ( &sem );
    exit ( EXIT_SUCCESS );
}

void *thread_function ( void *arg ) {
    sem_wait ( &sem );

    while ( strncmp ( "end", buffer, 3 ) != 0 ) {
        printf ( "You input %d characters\n", strlen ( buffer ) );
        sem_wait ( &sem );
    }

    pthread_exit ( NULL );
}
```

编译这个程序：

``` cpp
gcc -D_REENTRANT thread2.c -o thread2 –lpthread
```

执行结果：

``` cpp
Input some text. Enter 'end' to finish
123
You input 3 characters
1234
You input 4 characters
12345
You input 5 characters
end

Waiting for thread to finish...
Thread join
```

通过使用信号量，我们阻塞了统计字符个数的线程，这个程序似乎对快速的文本输入和悠闲的暂停都很适用，比之前的轮询解决方案效率上有了本质的提高。

##### 用互斥量进行线程同步

&emsp;&emsp;另一种用在多线程程序中同步访问的方法是使用互斥量。它允许程序员锁住某个对象，使得每次只能有一个线程访问它。为了控制对关键代码的访问，必须在进入这段代码之前锁住一个互斥量，然后在完成操作之后解锁它。用于互斥量的基本函数和用于信号量的函数非常相似：

``` cpp
#include <pthread.h>
int pthread_mutex_init ( pthread_mutex_t *mutex, const pthread_mutexattr_t, *mutexattr );
int pthread_mutex_lock ( pthread_mutex_t *mutex );
int pthread_mutex_unlock ( pthread_mutex_t *mutex );
int pthread_mutex_destory ( pthread_mutex_t *mutex );
```

与其他函数一样，成功时返回`0`，失败时将返回错误代码，但这些函数并不设置`errno`，所以必须对函数的返回代码进行检查。互斥量的属性设置这里不讨论，因此设置成`NULL`。我们用互斥量来重写刚才的代码如下：

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <semaphore.h>

#define SIZE 1024

char buffer[SIZE];
void *thread_function ( void *arg );
pthread_mutex_t mutex;

int main() {
    int res;
    pthread_t a_thread;
    void *thread_result;
    res = pthread_mutex_init ( &mutex, NULL );

    if ( res != 0 ) {
        perror ( "Mutex init failed!" );
        exit ( EXIT_FAILURE );
    }

    res = pthread_create ( &a_thread, NULL, thread_function, NULL );

    if ( res != 0 ) {
        perror ( "Thread create failed!" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Input some text. Enter 'end' to finish\n" );

    while ( 1 ) {
        pthread_mutex_lock ( &mutex );
        scanf ( "%s", buffer );
        pthread_mutex_unlock ( &mutex );

        if ( strncmp ( "end", buffer, 3 ) == 0 ) {
            break;
        }

        sleep ( 1 );
    }

    res = pthread_join ( a_thread, &thread_result );

    if ( res != 0 ) {
        perror ( "Thread join failed!" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Thread joined\n" );
    pthread_mutex_destroy ( &mutex );
    exit ( EXIT_SUCCESS );
}

void *thread_function ( void *arg ) {
    sleep ( 1 );

    while ( 1 ) {
        pthread_mutex_lock ( &mutex );
        printf ( "You input %d characters\n", strlen ( buffer ) );
        pthread_mutex_unlock ( &mutex );

        if ( strncmp ( "end", buffer, 3 ) == 0 ) {
            break;
        }

        sleep ( 1 );
    }
}
```

编译这个程序：

``` cpp
gcc -D_REENTRANT thread4.c -o thread4 –lpthread
```

执行结果：

``` cpp
Input some text. Enter 'end' to finish
123
You input 3 characters
1234
You input 4 characters
12345
You input 5 characters
end
You input 3 characters
Thread joined
```

#### 线程的属性

&emsp;&emsp;之前我们并未谈及到线程的属性，可以控制的线程属性是非常多的，这里面只列举一些常用的。如在前面的示例中，我们都使用的`pthread_join`同步线程，但其实有些情况下，我们并不需要。例如，主线程为服务线程，而第二个线程为数据备份线程，备份工作完成之后，第二个线程可以直接终止了，它没有必要再返回到主线程中。因此，我们可以创建一个`脱离线程`。下面介绍几个常用的函数：

``` cpp
int pthread_attr_init ( pthread_attr_t *attr );
```

功能是对线程属性变量的初始化。参数`attr`是线程属性。函数返回值为`0`表示成功，为`-1`表示失败。

``` cpp
int pthread_attr_setscope ( pthread_attr_t *attr, int scope );
```

功能是设置线程绑定属性。

- `attr`：线程属性。
- `scope`：`PTHREAD_SCOPE_SYSTEM`(绑定)和`PTHREAD_SCOPE_PROCESS`(非绑定)。

函数返回值为`0`表示成功，为`-1`表示失败。

``` cpp
int pthread_attr_setdetachstate ( pthread_attr_t *attr, int detachstate );
```

功能为设置线程分离属性。

- `attr`：线程属性。
- `detachstate`：`PTHREAD_CREATE_DETACHED`(分离)和`PTHREAD_CREATE_JOINABLE`(非分离)。

函数返回值为`0`表示成功，为`-1`表示失败。

``` cpp
int pthread_attr_setschedpolicy ( pthread_attr_t *attr, int policy );
```

功能为设置创建线程的调度策略。

- `attr`：线程属性。
- `policy`：线程调度策略，即`SCHED_FIFO`、`SCHED_RR`和`SCHED_OTHER`。

函数返回值为`0`表示成功，为`-1`表示失败。

``` cpp
int pthread_attr_setschedparam ( pthread_attr_t *attr, struct sched_param *param );
```

功能为设置线程优先级。

- `attr`：线程属性。
- `param`：线程优先级。

函数返回值为`0`表示成功，为`-1`表示失败。

``` cpp
int pthread_attr_destroy ( pthread_attr_t *attr );
```

功能为对线程属性变量的销毁。参数`attr`为线程属性。函数返回值为`0`表示成功，为`-1`表示失败。

#### 其他函数

&emsp;&emsp;其他函数如下：

``` cpp
/* 设置新创建线程栈的保护区大小 */
int pthread_attr_setguardsize ( pthread_attr_t *attr, size_t guardsize );
/* 决定怎样设置新创建线程的调度属性 */
int pthread_attr_setinheritsched ( pthread_attr_t *attr, int inheritsched );
/* 两者共同决定了线程栈的基地址以及堆栈的最小尺寸(以字节为单位) */
int pthread_attr_setstack ( pthread_attr_t *attr, void *stackader, size_t stacksize );
/* 决定了新创建线程的栈的基地址 */
int pthread_attr_setstackaddr ( pthread_attr_t *attr, void *stackader );
/* 决定了新创建线程的栈的最小尺寸(以字节为单位) */
int pthread_attr_setstacksize ( pthread_attr_t *attr, size_t stacksize );
```

例如创建优先级为`10`的线程：

``` cpp
pthread_attr_t attr;
struct sched_param param;
pthread_attr_init ( &attr );
pthread_attr_setscope ( &attr, PTHREAD_SCOPE_SYSTEM ); /* 绑定 */
pthread_attr_setdetachstate ( &attr, PTHREAD_CREATE_DETACHED ); /* 分离 */
pthread_attr_setschedpolicy ( &attr, SCHED_RR );
param.sched_priority = 10;
pthread_attr_setschedparam ( &attr, &param );
pthread_create ( xxx, &attr, xxx, xxx );
pthread_attr_destroy ( &attr );
```

下面实现一个脱离线程的程序，创建一个线程，其属性设置为脱离状态。子线程结束时，要使用pthread_exit，原来的主线程不再等待与子线程重新合并。

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <pthread.h>

void *thread_function ( void *arg );
char message[] = "Hello World";
int thread_finished = 0;

int main() {
    int res;
    pthread_t a_thread;
    pthread_attr_t thread_attr;
    res = pthread_attr_init ( &thread_attr );

    if ( res != 0 ) {
        perror ( "Attribute creation failed" );
        exit ( EXIT_FAILURE );
    }

    res = pthread_attr_setdetachstate ( &thread_attr, PTHREAD_CREATE_DETACHED );

    if ( res != 0 ) {
        perror ( "Setting detached attribute failed" );
        exit ( EXIT_FAILURE );
    }

    res = pthread_create ( &a_thread, &thread_attr, thread_function, ( void * ) message );

    if ( res != 0 ) {
        perror ( "Thread creation failed" );
        exit ( EXIT_FAILURE );
    }

    pthread_attr_destroy ( &thread_attr );

    while ( !thread_finished ) {
        printf ( "Waiting for thread to say it's finished...\n" );
        sleep ( 1 );
    }

    printf ( "Other thread finished, bye!\n" );
    exit ( EXIT_SUCCESS );
}

void *thread_function ( void *arg ) {
    printf ( "thread_function is running. Argument was %s\n", ( char * ) arg );
    sleep ( 4 );
    printf ( "Second thread setting finished flag, and exiting now\n" );
    thread_finished = 1;
    pthread_exit ( NULL );
}
```

编译这个程序：

``` cpp
gcc -D_REENTRANT thread5.c -o thread5 –lpthread
```

执行结果：

``` cpp
thread_function is running. Argument: hello world!
Waiting for thread to finished...
Waiting for thread to finished...
Waiting for thread to finished...
Waiting for thread to finished...
Second thread setting finished flag, and exiting now
Other thread finished!
```

通过设置线程的属性，我们还可以控制线程的调试，其方式与设置脱离状态是一样的。

### 取消一个线程

&emsp;&emsp;有时，我们想让一个线程可以要求另一个线程终止，线程有方法做到这一点，与信号处理一样，线程可以在被要求终止时改变其行为。先来看用于请求一个线程终止的函数：

``` cpp
#include <pthread.h>
int pthread_cancel ( pthread_t thread );
```

这个函数简单易懂，提供一个线程标识符，我们就可以发送请求来取消它。
&emsp;&emsp;线程可以用`pthread_setcancelstate`设置自己的取消状态：

``` cpp
#include <pthread.h>
int pthread_setcancelstate ( int state, int *oldstate );
```

- `state`：可以是`PTHREAD_CANCEL_ENABLE`允许线程接收取消请求，也可以是`PTHREAD_CANCEL_DISABLE`忽略取消请求。
- `oldstate`：获取先前的取消状态。如果对它没兴趣，可以简单地设置为`NULL`。如果取消请求被接受了，线程可以进入第二个控制层次，用`pthread_setcanceltype`设置取消类型。

``` cpp
#include <pthread.h>
int pthread_setcanceltype ( int type, int *oldtype );
```

- `type`：可以取`PTHREAD_CANCEL_ASYNCHRONOUS`，它将使得在接收到取消请求后立即采取行动；另一个是`PTHREAD_CANCEL_DEFERRED`，它将使得在接收到取消请求后，一直等待直到线程执行了下述函数之一后才采取行动：`pthread_join`、`pthread_cond_wait`、`pthread_cond_timedwait`、`pthread_testcancel`、`sem_wait`或`sigwait`。
- `oldtype`：允许保存先前的状态，如果不想知道先前的状态，可以传递`NULL`。

默认情况下，线程在启动时的取消状态为`PTHREAD_CANCEL_ENABLE`，取消类型是`PTHREAD_CANCEL_DEFERRED`。
&emsp;&emsp;下面编写代码，主线程向它创建的线程发送一个取消请求：

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

void *thread_function ( void *arg );

int main() {
    int res;
    pthread_t a_thread;
    void *thread_result;
    res = pthread_create ( &a_thread, NULL, thread_function, NULL );

    if ( res != 0 ) {
        perror ( "Thread create failed!" );
        exit ( EXIT_FAILURE );
    }

    sleep ( 4 );
    printf ( "Canceling thread...\n" );
    res = pthread_cancel ( a_thread );

    if ( res != 0 ) {
        perror ( "Thread cancel failed!" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Waiting for thread to finished...\n" );
    res = pthread_join ( a_thread, &thread_result );

    if ( res != 0 ) {
        perror ( "Thread join failed!" );
        exit ( EXIT_FAILURE );
    }

    printf ( "Thread canceled!\n" );
    exit ( EXIT_FAILURE );
}

void *thread_function ( void *arg ) {
    int i;
    int res;
    res = pthread_setcancelstate ( PTHREAD_CANCEL_ENABLE, NULL );

    if ( res != 0 ) {
        perror ( "Thread setcancelstate failed!" );
        exit ( EXIT_FAILURE );
    }

    res = pthread_setcanceltype ( PTHREAD_CANCEL_DEFERRED, NULL );

    if ( res != 0 ) {
        perror ( "Thread setcanceltype failed!" );
        exit ( EXIT_FAILURE );
    }

    printf ( "thread_function is running...\n" );

    for ( i = 0; i < 10; i++ ) {
        printf ( "Thread is still running (%d)...\n", i );
        sleep ( 1 );
    }

    pthread_exit ( 0 );
}
```

编译这个程序：

``` cpp
gcc -D_REENTRANT thread6.c -o thread6 –lpthread
```

执行结果：

``` cpp
thread_function is running...
Thread is still running (0)...
Thread is still running (1)...
Thread is still running (2)...
Thread is still running (3)...
Canceling thread...
Waiting for thread to finished...
Thread canceled!
```

### 多线程

&emsp;&emsp;之前所编写的代码里面都仅仅是创建了一个线程，现在来演示如何创建一个多线程的程序：

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

#define NUM 6

void *thread_function ( void *arg );

int main() {
    int res;
    pthread_t a_thread[NUM];
    void *thread_result;
    int index;

    for ( index = 0; index < NUM; ++index ) {
        res = pthread_create ( &a_thread[index], NULL, thread_function, ( void * ) index );

        if ( res != 0 ) {
            perror ( "Thread create failed!" );
            exit ( EXIT_FAILURE );
        }

        sleep ( 1 );
    }

    printf ( "Waiting for threads to finished...\n" );

    for ( index = NUM - 1; index >= 0; --index ) {
        res = pthread_join ( a_thread[index], &thread_result );

        if ( res == 0 ) {
            printf ( "Picked up a thread:%d\n", index + 1 );
        } else {
            perror ( "pthread_join failed\n" );
        }
    }

    printf ( "All done\n" );
    exit ( EXIT_SUCCESS );
}

void *thread_function ( void *arg ) {
    int my_number = ( int ) arg;
    int rand_num;
    printf ( "thread_function is running. Argument was %d\n", my_number );
    rand_num = 1 + ( int ) ( 9.0 * rand() / ( RAND_MAX + 1.0 ) );
    sleep ( rand_num );
    printf ( "Bye from %d\n", my_number );
    pthread_exit ( NULL );
}
```

编译这个程序：

``` cpp
gcc -D_REENTRANT thread7.c -o thread7 –lpthread
```

执行结果：

``` cpp
thread_function is running. Argument was 0
thread_function is running. Argument was 1
thread_function is running. Argument was 2
thread_function is running. Argument was 3
thread_function is running. Argument was 4
Bye from 1
thread_function is running. Argument was 5
Waiting for threads to finished...
Bye from 5
Picked up a thread:6
Bye from 0
Bye from 2
Bye from 3
Bye from 4
Picked up a thread:5
Picked up a thread:4
Picked up a thread:3
Picked up a thread:2
Picked up a thread:1
All done
```

---

### linux的pthread_kill函数

&emsp;&emsp;该函数可以用于向指定的线程发送信号：

``` cpp
int pthread_kill ( pthread_t threadId, int signal );
```

如果线程内不对信号进行处理，则调用默认的处理程式，如`SIGQUIT`会退出终止线程，`SIGKILL`会杀死线程，可以调用`signal(SIGQUIT, sig_process_routine);`来自定义信号的处理程序。
&emsp;&emsp;`pthread_kill`返回值如下：

返回值   | 说明
---------|-----
`0`      | 调用成功
`ESRCH`  | 线程不存在
`EINVAL` | 信号不合法

``` cpp
int kill_ret = pthread_kill ( thread_id, 0 );

if ( kill_ret == ESRCH ) {
    printf ( "指定的线程不存在或者是已经终止\n" );
} else if ( kill_ret == EINVAL ) {
    printf ( "调用传递一个无用的信号\n" );
} else {
    printf ( "线程存在\n" );
}
```

---

### Linux多线程的分离与结合

&emsp;&emsp;在任何一个时间点上，线程是可结合的(`joinable`)或者是分离的(`detached`)。一个可结合的线程能够被其他线程收回其资源和杀死；在被其他线程回收之前，它的存储器资源(例如`栈`)是不释放的。相反，一个分离的线程是不能被其他线程回收或杀死的，它的存储器资源在它终止时由系统自动释放。
&emsp;&emsp;线程的分离状态决定一个线程以什么样的方式来终止自己。线程的默认属性为非分离状态，这种情况下，原有的线程等待创建的线程结束。只有当`pthread_join`函数返回时，创建的线程才算终止，才能释放自己占用的系统资源。而分离线程没有被其他的线程所等待，自己运行结束了，线程也就终止了，马上释放系统资源。程序员应该根据自己的需要，选择适当的分离状态。
&emsp;&emsp;设置线程分离状态的函数为：

``` cpp
int pthread_attr_setdetachstate ( pthread_attr_t *attr, int detachstate );
```

参数`detachstate`可选为`PTHREAD_CREATE_DETACHED`(分离线程)和`PTHREAD_CREATE_JOINABLE`(非分离线程)。查看线程的分离状态属性的函数如下：

``` cpp
#include <pthread.h>
int pthread_attr_getdetachstate ( const pthread_attr_t *attr, int *detachstate );
```

这里要注意的一点是，如果设置一个线程为分离线程，而这个线程运行又非常快，它很可能在`pthread_create`函数返回之前就终止了，它终止以后就可能将线程号和系统资源移交给其他的线程使用，这样调用`pthread_create`的线程就得到了错误的线程号。要避免这种情况可以采取一定的同步措施，最简单的方法之一是可以在被创建的线程里调用`pthread_cond_timewait`函数，让这个线程等待一会儿，留出足够的时间让函数`pthread_create`返回。设置一段等待时间，是在多线程编程里常用的方法。但是注意不要使用诸如`wait`之类的函数，它们是使整个进程睡眠，并不能解决线程同步的问题。
&emsp;&emsp;另外一个可能常用的属性是线程的优先级，它存放在结构`sched_param`中。用函数`pthread_attr_getschedparam`和函数`pthread_attr_setschedparam`进行存放，一般说来，我们总是先取优先级，对取得的值修改后再存放回去。
&emsp;&emsp;能够处理线程终止的函数有如下几个：

``` cpp
#include <pthread.h>
void pthread_exit ( void *retval );
void pthread_join ( pthread_t th, void *thread_return );
int pthread_detach ( pthread_t th );
```

如果线程处于`joinable`状态，则只能被创建它的线程等待终止。
&emsp;&emsp;在`Linux`平台，默认情况下虽然各个线程之间是相互独立的，一个线程的终止不会去通知或影响其他的线程。但是已经终止的线程的资源并不会随着线程的终止而得到释放，我们需要调用`pthread_join`来获得另一个线程的终止状态并且释放该线程所占的资源。调用该函数的线程将挂起，等待`th`所表示的线程的结束。`thread_return`是指向线程`th`返回值的指针。需要注意的是`th`所表示的线程必须是`joinable`的，即处于非`detached`状态；并且只可以有唯一的一个线程对`th`调用`pthread_join`。如果`th`处于`detached`状态，那么对`th`的`pthread_join`调用将返回错误。
&emsp;&emsp;如果不关心一个线程的结束状态，那么也可以将一个线程设置为`detached`状态，从而让操作系统在该线程结束时来回收它所占的资源。将一个线程设置为`detached`状态可以通过两种方式来实现：一种是调用`pthread_detach`函数，可以将线程`th`设置为`detached`状态；另一种方法是在创建线程时就将它设置为`detached`状态，首先初始化一个线程属性变量，然后将其设置为`detached`状态，最后将它作为参数传入线程创建函数`pthread_create`，这样所创建出来的线程就直接处于`detached`状态。创建`detach`线程的代码如下：

``` cpp
pthread_t tid;
pthread_attr_t attr;
pthread_attr_init ( &attr );
pthread_attr_setdetachstate ( &attr, PTHREAD_CREATE_DETACHED );
pthread_create ( &tid, &attr, THREAD_FUNCTION, arg );
```

总之为了在使用`pthread`时避免线程的资源在线程结束时不能得到正确释放，从而避免产生潜在的内存泄漏问题，在对待线程结束时，要确保该线程处于`detached`状态，否着就需要调用`pthread_join`函数来对其进行资源回收。

---

### 线程的继承性

&emsp;&emsp;函数`pthread_attr_setinheritsched`和`pthread_attr_getinheritsched`分别用来设置和得到线程的继承性：

``` cpp
#include <pthread.h>
int pthread_attr_getinheritsched ( const pthread_attr_t *attr, int *inheritsched );
int pthread_attr_setinheritsched ( pthread_attr_t *attr, int inheritsched );
```

- `attr`：线程属性变量。
- `inheritsched`：线程的继承性。

若成功返回`0`，若失败返回`-1`。
&emsp;&emsp;继承性决定调度的参数是从创建的进程中继承还是使用在`schedpolicy`和`schedparam`属性中显式设置的调度信息。线程没有默认的继承值设置，所以如果关心线程的调度策略和参数，只能手动设置。可设置的参数如下：

- `PTHREAD_INHERIT_SCHED`：新的线程继承创建线程的策略和参数。
- `PTHREAD_EXPLICIT_SCHED`：新的线程继承策略和参数来自于`schedpolicy`和`schedparam`属性中显式设置的调度信息。

### 线程的调度策略

&emsp;&emsp;函数`pthread_attr_setschedpolicy`和`pthread_attr_getschedpolicy`分别用来设置和得到线程的调度策略：

``` cpp
int pthread_attr_getschedpolicy ( const pthread_attr_t *attr, int *policy );
int pthread_attr_setschedpolicy ( pthread_attr_t *attr, int policy );
```

- `attr`：线程属性变量指针。
- `policy`：调度策略。

若成功返回`0`，若失败返回`-1`。
&emsp;&emsp;所谓调度策略也就是`OS`中那些调度算法：

- `SCHED_FIFO`：先进先出。
- `SCHED_RR`：轮转法。
- `SCHED_OTHER`：其他方法。

&emsp;&emsp;`SCHED_OTHER`是不支持优先级使用的，而`SCHED_FIFO`和`SCHED_RR`支持优先级的使用，它们分别为`1`和`99`，数值越大优先级越高。注意，此处的`SCHED_FIFO`是允许被高优先级抢占的，也就是有高优先级的必须先运行；`SCHED_RR`是设置一个时间片。当有`SCHED_FIFO`或`SCHED_RR`策略的线程在一个条件变量上等持或等持加锁同一个互斥量时，它们将以优先级顺序被唤醒。即如果一个低优先级的`SCHED_FIFO`线程和一个高优先织的`SCHED_FIFO`线程都在等待锁相同的互斥且，则当互斥量被解锁时，高优先级线程将总是被首先解除阻塞。

### 线程的调度参数

&emsp;&emsp;函数`pthread_attr_getschedparam`和`pthread_attr_setschedparam`分别用来设置和得到线程的调度参数：

``` cpp
int pthread_attr_getschedparam ( const pthread_attr_t *attr, struct sched_param *param );
int pthread_attr_setschedparam ( pthread_attr_t *attr, const struct sched_param *param );
```

- `attr`：线程变量属性。
- `param`：`sched_parm`结构体。

若成功返回`0`，若失败返回`-1`。`sched_param`结构体在文件`/usr/include /bits/sched.h`中定义：

``` cpp
struct sched_param {
    int sched_priority; /* 参数的本质就是优先级 */
};
```

注意，大的权值对应高的优先级。系统支持的最大和最小的优先级值可以用函数`sched_get_priority_max`和`sched_get_priority_min`得到：

``` cpp
#include <pthread.h>
int sched_get_priority_max ( int policy );
int sched_get_priority_min ( int policy );
```

参数是`policy`调用策略，也就是说对于不同的策略的值是不一样的。使用实例如下：

``` cpp
max_ = sched_get_priority_max ( policy );
min_ = sched_get_priority_min ( policy );
```

建议不修改线程的优先级，否则可能会出现死锁的现象。

### 线程的作用域

&emsp;&emsp;函数`pthread_attr_setscope`和`pthread_attr_getscope`分别用来设置和得到线程的作用域：

``` cpp
#include <pthread.h>
int pthread_attr_getscope ( const pthread_attr_t *attr, int *scope );
int pthread_attr_setscope ( pthread_attr_t *attr, int scope );
```

- `attr`：线程属性变量。
- `scope`：线程的作用域。

若成功返回`0`，若失败返回`-1`。
&emsp;&emsp;作用域控制线程是否在进程内或在系统级上竞争资源，可能的值如下所示：

- `PTHREAD_SCOPE_PROCESS`：进程内竞争资源。
- `PTHREAD_SCOPE_SYSTEM`：系统级竞争资源。

### 线程堆栈的大小

&emsp;&emsp;函数`pthread_attr_setstackaddr`和`pthread_attr_getstackaddr`分别用来设置和得到线程堆栈的位置：

``` cpp
int pthread_attr_getstacksize ( const pthread_attr_t *attr, size_t *stacksize );
int pthread_attr_setstacksize ( pthread_attr_t *attr, size_t *stacksize );
```

- `attr`：线程属性变量。
- `stacksize`：堆栈大小。

若成功返回`0`，若失败返回`-1`。

### 线程堆栈的地址

&emsp;&emsp;函数`pthread_attr_getstackaddr`和`pthread_attr_setstackaddr`函数原型如下：

``` cpp
#include <pthread.h>
int pthread_attr_getstackaddr ( const pthread_attr_t *attr, void **stackaddr );
int pthread_attr_setstackaddr ( pthread_attr_t *attr, void *stackaddr );
```

- `attr`：线程属性变量。
- `stackaddr`：堆栈地址。

若成功返回`0`，若失败返回`-1`。注意，`pthread_attr_getstackaddr`已经过期，现在使用的是`pthread_attr_getstack`。

### 警戒缓冲区

&emsp;&emsp;函数`pthread_attr_getguardsize`和`pthread_attr_setguardsize`分别用来得到和设置线程栈末尾的警戒缓冲区大小：

``` cpp
#include <pthread.h>
int pthread_attr_getguardsize ( const pthread_attr_t *restrict attr, size_t *restrict guardsize );
int pthread_attr_setguardsize ( pthread_attr_t *attr, size_t *guardsize );
```

若成功返回`0`，若失败返回`-1`。注意，线程属性`guardsize`控制着线程栈末尾之后以避免栈溢出的扩展内存大小，这个属性默认设置为`PAGESIZE`个字节。可以把`guardsize`线程属性设为`0`，从而不允许属性的这种特征行为发生：在这种情况下不会提供警戒缓存区。同样地，如果对线程属性`stackaddr`作了修改，系统就会认为我们会自己管理栈，并使警戒栈缓冲区机制无效，等同于把`guardsize`线程属性设为`0`。

``` cpp
#include <string.h>
#include <pthread.h>
#include <sched.h>
#include <stdio.h>

void *child_thread ( void *arg ) {
    int policy = 0;
    int max_priority = 0, min_priority = 0;
    struct sched_param param;
    pthread_attr_t attr;
    pthread_attr_init ( &attr ); /* 初始化线程属性变量 */
    /* 设置线程的继承性 */
    pthread_attr_setinheritsched ( &attr, PTHREAD_EXPLICIT_SCHED );
    pthread_attr_getinheritsched ( &attr, &policy ); /* 获得线程的继承性 */

    if ( policy == PTHREAD_EXPLICIT_SCHED ) {
        printf ( "Inheritsched: PTHREAD_EXPLICIT_SCHED\n" );
    }

    if ( policy == PTHREAD_INHERIT_SCHED ) {
        printf ( "Inheritsched: PTHREAD_INHERIT_SCHED\n" );
    }

    /* 设置线程调度策略 */
    pthread_attr_setschedpolicy ( &attr, SCHED_RR );
    /* 获得线程调度策略 */
    pthread_attr_getschedpolicy ( &attr, &policy );

    if ( policy == SCHED_FIFO ) {
        printf ( "Schedpolicy: SCHED_FIFO\n" );
    }

    if ( policy == SCHED_RR ) {
        printf ( "Schedpolicy: SCHED_RR\n" );
    }

    if ( policy == SCHED_OTHER ) {
        printf ( "Schedpolicy: SCHED_OTHER\n" );
    }

    /* 获得系统支持的线程优先权的最大值 */
    max_priority = sched_get_priority_max ( policy );
    /* 获得系统支持的线程优先权的最小值 */
    min_priority = sched_get_priority_min ( policy );
    printf ( "Maxpriority: %u\n", max_priority );
    printf ( "Minpriority: %u\n", min_priority );
    param.sched_priority = max_priority;
    /* 设置线程的调度参数 */
    pthread_attr_setschedparam ( &attr, &param );
    /* 获得线程的调度参数 */
    printf ( "sched_priority: %u\n", param.sched_priority );
    pthread_attr_destroy ( &attr );
}

int main ( int argc, char *argv[] ) {
    pthread_t child_thread_id;
    pthread_create ( &child_thread_id, NULL, child_thread, NULL );
    pthread_join ( child_thread_id, NULL );
}
```

执行结果：

``` cpp
Inheritsched: PTHREAD_EXPLICIT_SCHED
Schedpolicy: SCHED_RR
Maxpriority: 99
Minpriority: 1
sched_priority: 99
```

---

### 互斥锁pthread_mutex_t

#### 锁的创建

&emsp;&emsp;锁可以被动态或静态创建，可以用宏`PTHREAD_MUTEX_INITIALIZER`来静态的初始化锁，采用这种方式比较容易理解。互斥锁是`pthread_mutex_t`的结构体，而这个宏是一个结构常量，如下代码可以完成静态的初始化锁：

``` cpp
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
```

另外锁可以用`pthread_mutex_init`函数动态的创建：

``` cpp
int pthread_mutex_init ( pthread_mutex_t *mutex, const pthread_mutexattr_t *attr )
```

#### 锁的属性

&emsp;&emsp;互斥锁属性可以由`pthread_mutexattr_init`函数来初始化，然后调用其他的属性设置方法来设置其属性。互斥锁的范围：可以指定是该进程与其他进程的同步还是同一进程内不同的线程之间的同步。可以设置为`PTHREAD_PROCESS_SHARE`和`PTHREAD_PROCESS_PRIVATE`。默认是后者，表示进程内使用锁。可以使用如下函数设置与获取锁的范围：

``` cpp
int pthread_mutexattr_setpshared ( pthread_mutexattr_t *mattr, int pshared );
int pthread_mutexattr_getshared ( pthread_mutexattr_t *mattr, int *pshared );
```

&emsp;&emsp;互斥锁的类型如下：

- `PTHREAD_MUTEX_TIMED_NP`：这是缺省值，也就是普通锁。当一个线程加锁以后，其余请求锁的线程将形成一个等待队列，并在解锁后按优先级获得锁。这种锁策略保证了资源分配的公平性。
- `PTHREAD_MUTEX_RECURSIVE_NP`：嵌套锁，允许同一个线程对同一个锁成功获得多次，并通过多次`unlock`解锁。如果是不同线程请求，则在加锁线程解锁时重新竞争。
- `PTHREAD_MUTEX_ERRORCHECK_NP`：检错锁，如果同一个线程请求同一个锁，则返回`EDEADLK`，否则与`PTHREAD_MUTEX_TIMED_NP`类型动作相同。这样就保证当不允许多次加锁时不会出现最简单情况下的死锁。
- `PTHREAD_MUTEX_ADAPTIVE_NP`：适应锁，动作最简单的锁类型，仅等待解锁后重新竞争。

可以用如下函数获取或设置锁的类型：

``` cpp
int pthread_mutexattr_settype ( pthread_mutexattr_t *attr, int type );
int pthread_mutexattr_gettype ( pthread_mutexattr_t *attr, int *type );
```

---

### 线程条件变量pthread_cond_t

&emsp;&emsp;1. 使用`pthread_cond_init`初始化条件变量：

``` cpp
#include <pthread.h>
int pthread_cond_init ( pthread_cond_t *cv, const pthread_condattr_t *cattr );
```

函数执行成功返回`0`，任何其他返回值都表示错误。当参数`cattr`为空指针时，函数创建的是一个缺省的条件变量，否则条件变量的属性将由`cattr`中的属性值来决定。调用`pthread_cond_init`函数时，参数`cattr`为空指针等价于`cattr`中的属性为缺省属性，只是前者不需要`cattr`所占用的内存开销。这个函数返回时，条件变量被存放在参数`cv`指向的内存中。
&emsp;&emsp;可以用宏`PTHREAD_COND_INITIALIZER`来初始化静态定义的条件变量，使其具有缺省属性，这和用`pthread_cond_init`函数动态分配的效果是一样的。初始化时不进行错误检查。

``` cpp
pthread_cond_t cv = PTHREAD_COND_INITIALIZER;
```

不能由多个线程同时初始化一个条件变量。当需要重新初始化或释放一个条件变量时，应用程序必须保证这个条件变量未被使用。

&emsp;&emsp;2. 使用`pthread_cond_wait`阻塞在条件变量上：

``` cpp
#include <pthread.h>
int pthread_cond_wait ( pthread_cond_t *cv, pthread_mutex_t *mutex );
```

函数执行成功返回`0`，任何其他返回值都表示错误。函数将解锁`mutex`参数指向的互斥锁，并使当前线程阻塞在`cv`参数指向的条件变量上。被阻塞的线程可以被`pthread_cond_signal`、`pthread_cond_broadcast`函数唤醒，也可能在被信号中断后被唤醒。
&emsp;&emsp;`pthread_cond_wait`函数的返回并不意味着条件的值一定发生了变化，必须重新检查条件的值。`pthread_cond_wait`函数返回时，相应的互斥锁将被当前线程锁定，即使是函数出错返回。
&emsp;&emsp;一般情况下，一个条件表达式都是在一个互斥锁的保护下被检查。当条件表达式未被满足时，线程将仍然阻塞在这个条件变量上。当另一个线程改变了条件的值并向条件变量发出信号时，等待在这个条件变量上的一个线程或所有线程被唤醒，接着都试图再次占有相应的互斥锁。
&emsp;&emsp;阻塞在条件变量上的线程被唤醒以后，直到`pthread_cond_wait`函数返回之前条件的值都有可能发生变化。所以函数返回以后，在锁定相应的互斥锁之前，必须重新测试条件值。最好的测试方法是循环调用`pthread_cond_wait`函数，并把满足条件的表达式置为循环的终止条件。

``` cpp
pthread_mutex_lock();

while ( condition_is_false ) {
    pthread_cond_wait();
}

pthread_mutex_unlock();
```

阻塞在同一个条件变量上的不同线程被释放的次序是不一定的。
&emsp;&emsp;注意，`pthread_cond_wait`函数是退出点，如果在调用这个函数时，已有一个挂起的退出请求，且线程允许退出，这个线程将被终止并开始执行善后处理函数，而这时和条件变量相关的互斥锁仍将处在锁定状态。

&emsp;&emsp;3. 使用`pthread_cond_signal`解除在条件变量上的阻塞：

``` cpp
#include <pthread.h>
int pthread_cond_signal ( pthread_cond_t *cv );
```

函数执行成功返回`0`，任何其他返回值都表示错误。函数被用来释放被阻塞在指定条件变量上的一个线程。必须在互斥锁的保护下使用相应的条件变量。否则对条件变量的解锁有可能发生在锁定条件变量之前，从而造成死锁。唤醒阻塞在条件变量上的所有线程的顺序由调度策略决定，如果线程的调度策略是`SCHED_OTHER`类型的，系统将根据线程的优先级唤醒线程。如果没有线程被阻塞在条件变量上，那么调用`pthread_cond_signal`将没有作用。
&emsp;&emsp;4. 使用`pthread_cond_timedwait`阻塞直到指定时间：

``` cpp
#include <pthread.h>
#include <time.h>
int pthread_cond_timedwait (
    pthread_cond_t *cv, pthread_mutex_t *mp,
    const structtimespec *abstime
);
```

函数执行成功返回`0`，任何其他返回值都表示错误。函数到了一定的时间，即使条件未发生也会解除阻塞，这个时间由参数`abstime`指定。函数返回时，相应的互斥锁往往是锁定的，即使是函数出错返回。注意，`pthread_cond_timedwait`函数也是退出点。
&emsp;&emsp;超时时间参数是指一天中的某个时刻：

``` cpp
pthread_timestruc_t to;
to.tv_sec = time ( NULL ) + TIMEOUT;
to.tv_nsec = 0;
```

超时返回的错误码是`ETIMEDOUT`。

&emsp;&emsp;5. 使用`pthread_cond_broadcast`释放阻塞的所有线程：

``` cpp
#include <pthread.h>
int pthread_cond_broadcast ( pthread_cond_t *cv );
```

函数执行成功返回`0`，任何其他返回值都表示错误。函数唤醒所有被`pthread_cond_wait`函数阻塞在某个条件变量上的线程，参数`cv`被用来指定这个条件变量。当没有线程阻塞在这个条件变量上时，`pthread_cond_broadcast`函数无效。由于`pthread_cond_broadcast`函数唤醒所有阻塞在某个条件变量上的线程，这些线程被唤醒后将再次竞争相应的互斥锁，所以必须小心使用`pthread_cond_broadcast`函数。

&emsp;&emsp;6. 使用`pthread_cond_destroy`释放条件变量：

``` cpp
#include <pthread.h>
int pthread_cond_destroy ( pthread_cond_t *cv );
```

函数执行成功返回`0`，任何其他返回值都表示错误。该函数用于释放条件变量。注意，条件变量占用的空间并未被释放。

&emsp;&emsp;7. 唤醒丢失问题：在线程未获得相应的互斥锁时调用`pthread_cond_signal`或`pthread_cond_broadcast`函数可能会引起唤醒丢失问题。唤醒丢失往往会在下面的情况下发生：

- 一个线程调用`pthread_cond_signal`或`pthread_cond_broadcast`函数。
- 另一个线程正处在测试条件变量和调用`pthread_cond_wait`函数之间。
- 没有线程正在处在阻塞等待的状态下。

``` cpp
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_mutex_t count_lock; /* 自旋锁 */
pthread_cond_t count_nonzero; /* 条件锁 */
unsigned count = 0;

void *decrement_count ( void *arg ) {
    /* 等待线程，使用pthread_cond_wait前要先加锁 */
    pthread_mutex_lock ( &count_lock );
    printf ( "decrement_count get count_lock\n" );

    while ( count == 0 ) {
        printf ( "decrement_count count == 0\n" );
        printf ( "decrement_count before cond_wait\n" );
        /* pthread_cond_wait内部会解锁，然后等待条件变量被其它线程激活 */
        pthread_cond_wait ( &count_nonzero, &count_lock );
        printf ( "decrement_count after cond_wait\n" );
    }

    printf ( "tid1: count--\n" );
    count = count - 1;
    printf ( "tid1: count = %d\n", count );
    pthread_mutex_unlock ( &count_lock );
}

void *increment_count ( void *arg ) {
    /* 激活线程：加锁(和等待线程用同一个锁) */
    pthread_mutex_lock ( &count_lock );
    printf ( "increment_count get count_lock\n" );

    if ( count == 0 ) {
        printf ( "increment_count before cond_signal\n" );
        /* pthread_cond_signal发送信号 */
        pthread_cond_signal ( &count_nonzero );
        printf ( "increment_count after cond_signal\n" );
    }

    printf ( "tid2: count++\n" );
    count = count + 1;
    printf ( "tid2: count = %d\n", count );
    /* 解锁：激活线程的上面三个操作在运行时间上都在等待线程的pthread_cond_wait函数内部 */
    pthread_mutex_unlock ( &count_lock );
}

int main ( void ) {
    pthread_t tid1, tid2;
    pthread_mutex_init ( &count_lock, NULL );
    pthread_cond_init ( &count_nonzero, NULL );
    pthread_create ( &tid1, NULL, decrement_count, NULL );
    printf ( "tid1 decrement is created, begin sleep 2s\n" );
    sleep ( 2 );
    printf ( "after sleep 2s, start creat tid2 increment\n" );
    pthread_create ( &tid2, NULL, increment_count, NULL );
    printf ( "after tid2 increment is created, begin sleep 10s\n" );
    sleep ( 5 );
    printf ( "after sleep 5s, begin exit!\n" );
    pthread_exit ( 0 );
    return 0;
}
```

执行结果：

``` cpp
tid1 decrement is created, begin sleep 2s
decrement_count get count_lock
decrement_count count == 0
decrement_count before cond_wait
after sleep 2s, start creat tid2 increment
after tid2 increment is created, begin sleep 10s
increment_count get count_lock
increment_count before cond_signal
increment_count after cond_signal
tid2: count++
tid2: count = 1
decrement_count after cond_wait
tid1: count--
tid1: count = 0
after sleep 5s, begin exit!
```

---

### 互斥锁与条件变量

&emsp;&emsp;`pthread_cond_wait`总和一个互斥锁结合使用，在调用`pthread_cond_wait`前要先获取锁。`pthread_cond_wait`函数执行时先自动释放指定的锁，然后等待条件变量的变化。在函数调用返回之前，自动将指定的互斥量重新锁住。

``` cpp
int pthread_cond_signal ( pthread_cond_t *cond );
```

`pthread_cond_signal`通过条件变量`cond`发送消息，若多个线程在等待，它只唤醒一个，而`pthread_cond_broadcast`可以唤醒所有。调用`pthread_cond_signal`后要立刻释放互斥锁，因为`pthread_cond_wait`的最后一步是要将指定的互斥量重新锁住，如果`pthread_cond_signal`之后没有释放互斥锁，`pthread_cond_wait`仍然要阻塞。
&emsp;&emsp;无论哪种等待方式，都必须和一个互斥锁配合，以防止多个线程同时请求`pthread_cond_wait`(或`pthread_cond_timedwait`)的竞争条件(`Race Condition`)。`mutex`互斥锁必须是普通锁(`PTHREAD_MUTEX_TIMED_NP`)或者适应锁(`PTHREAD_MUTEX_ADAPTIVE_NP`)，且在调用`pthread_cond_wait`前必须由本线程加锁(`pthread_mutex_lock()`)，而在更新条件等待队列以前，`mutex`保持锁定状态，并在线程挂起进入等待前解锁。在条件满足从而离开`pthread_cond_wait`之前，`mutex`将被重新加锁，与进入`pthread_cond_wait`前的加锁动作对应。
&emsp;&emsp;为什么在唤醒线程后要重新`mutex`加锁？了解`pthread_cond_wait`的作用非常重要，它是`POSIX`线程信号发送系统的核心，也是最难以理解的部分。首先，让我们考虑以下情况：线程为查看链表而锁定了互斥对象，然而该链表恰巧是空的。这一特定线程什么也干不了，其设计意图是从列表中除去节点，但是现在却没有节点。因此，它只能锁定互斥对象时，线程将调用`pthread_cond_wait(&mycond, &mymutex)`。`pthread_cond_wait`调用相当复杂，因此我们每次只执行它的一个操作。
&emsp;&emsp;`pthread_cond_wait`所做的第一件事就是对互斥对象解锁，于是其它线程可以修改链表，并等待条件`mycond`发生，这样当`pthread_cond_wait`接收到另一个线程的`信号`时，它将苏醒。现在互斥对象已被解锁，其它线程可以访问和修改链表，可能还会添加节点。此时，`pthread_cond_wait`调用还未返回。对互斥对象解锁会立即发生，但等待条件`mycond`通常是一个阻塞操作，这意味着线程将睡眠，在它苏醒之前不会消耗`CPU`周期，这正是我们期待发生的情况。线程将一直睡眠，直到特定条件发生，在这期间不会发生任何浪费`CPU`时间的繁忙查询。从线程的角度来看，它只是在等待`pthread_cond_wait`调用返回。
&emsp;&emsp;现在继续说明，假设另一个线程(称作`2`号线程)锁定了`mymutex`并对链表添加了一个节点。在对互斥对象解锁之后，`2`号线程会立即调用函数`pthread_cond_broadcast(&mycond)`。此操作之后，`2`号线程将使所有等待`mycond`条件变量的线程立即苏醒。这意味着第一个线程(仍处于`pthread_cond_wait`调用中)现在将苏醒。
&emsp;&emsp;现在，看一下第一个线程发生了什么。您可能会认为在`2`号线程调用`pthread_cond_broadcast(&mymutex)`之后，`1`号线程的`pthread_cond_wait`会立即返回。不是那样！实际上，`pthread_cond_wait`将执行最后一个操作，即重新锁定`mymutex`。一旦`pthread_cond_wait`锁定了互斥对象，那么它将返回并允许`1`号线程继续执行。来看一个例子：

``` cpp
/* In Thread1 */
pthread_mutex_lock ( &m_mutex );
pthread_cond_wait ( &m_cond, &m_mutex );
pthread_mutex_unlock ( &m_mutex );
/* In Thread2 */
pthread_mutex_lock ( &m_mutex );
pthread_cond_signal ( &m_cond );
pthread_mutex_unlock ( &m_mutex );
```

为什么要与`pthread_mutex`一起使用呢？这是为了应对线程`1`在调用`pthread_cond_wait`但线程1还没有进入`wait cond`的状态的时候，此时线程`2`调用了`pthread_cond_signal`的情况。如果不用`mutex`锁的话，这个`cond_singal`就丢失了。加了锁的情况是，线程`2`必须等到`mutex`被释放(也就是`pthread_cod_wait`释放锁并进入`wait_cond`状态，此时线程`2`上锁)的时候才能调用`pthread_cond_signal`。`pthread_cond_signal`既可以放在`pthread_mutex_lock`和`pthread_mutex_unlock`之间，也可以放在`pthread_mutex_lock`和`pthread_mutex_unlock`之后，但是各有有缺点。
&emsp;&emsp;在两者之间的情况如下：

``` cpp
pthread_mutex_lock
    xxxxxxx
pthread_cond_signal
pthread_mutex_unlock
```

缺点：在某个线程的实现中，会造成等待线程从内核中唤醒(由于`cond_signal`)然后又回到内核空间(因为`cond_wait`返回后会有原子加锁的行为)，所以一来一回会有性能的问题。但是在`Linux Threads`或者`NPTL`里面，就不会有这个问题，因为在`Linux`线程中有两个队列，分别是`cond_wait`队列和`mutex_lock`队列，`cond_signal`只是让线程从`cond_wait`队列移到`mutex_lock`队列，而不用返回到用户空间，不会有性能的损耗。所以在`Linux`中推荐使用这种模式。
&emsp;&emsp;在两者之后的情况如下：

``` cpp
pthread_mutex_lock
    xxxxxxx
pthread_mutex_unlock
pthread_cond_signal
```

优点：不会出现之前说的那个潜在的性能损耗，因为在`signal`之前就已经释放锁了。
缺点：如果`unlock`和`signal`之前，有个低优先级的线程正在`mutex`上等待的话，那么这个低优先级的线程就会抢占高优先级的线程(`cond_wait`的线程)，而这在上面的放中间的模式下是不会出现的。
&emsp;&emsp;**补充说明**：我在结束线程的时候，如果使用`pthread_kill`，有可能出现整个程序退出的现象。我的解决办法是：在线程中设置一个标志位，如果这个标志位被置`1`，则调用`pthread_exit`函数。