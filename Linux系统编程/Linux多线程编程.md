---
title: Linux多线程编程
categories: Linux系统编程
date: 2019-02-03 18:42:31
---
&emsp;&emsp;`Linux`进程创建一个新线程时，线程将拥有自己的栈，因为线程有自己的局部变量。但与它的创建者共享全局变量、文件描述符和当前目录状态。<!--more-->

### 进程与线程

&emsp;&emsp;在`Linux`系统下，启动一个新的进程必须分配给它独立的地址空间，建立众多的数据表来维护它的代码段、堆栈段和数据段，这是一种`昂贵`的多任务工作方式。
&emsp;&emsp;和进程相比，线程是一种非常`节俭`的多任务操作方式。运行于一个进程中的多个线程，它们之间使用相同的地址空间，而且线程间彼此切换所需的时间也远远小于进程间切换所需要的时间。
&emsp;&emsp;对不同进程来说，它们具有独立的数据空间，要进行数据的传递只能通过进程间通信的方式进行。而同一进程下的线程之间共享数据空间，所以一个线程的数据可以直接为其它线程所用。
&emsp;&emsp;进程可以看成一个资源的基本单位，而线程是程序调度的基本单位，一个进程内部的线程之间共享进程获得的时间片。
&emsp;&emsp;除了以上所说的优点外，多线程程序作为一种多任务、并发的工作方式，有如下优点：

- 使多`CPU`系统更加有效：操作系统会保证当线程数不大于`CPU`数目时，不同的线程运行于不同的`CPU`上。
- 改善程序结构：一个既长又复杂的进程可以考虑分为多个线程，成为几个独立或半独立的运行部分，这样的程序会利于理解和修改。

### 编译多线程

&emsp;&emsp;因为`pthread`的库不是`linux`系统的库，所以在进行编译的时候要加上`-lpthread`：

``` cpp
gcc main.c -o main -lpthread
```

### 线程标识

&emsp;&emsp;`pthread_self`的功能是获取线程的`ID`：

``` cpp
#include <pthread.h>
pthread_t pthread_self ( void );
```

### 线程创建

&emsp;&emsp;`pthread_create`函数原型如下，如果调用成功，则返回值是`0`，否则返回错误代码：

``` cpp
#include <pthread.h>
int pthread_create ( pthread_t *thread, pthread_attr_t *attr,
                     void * ( *start_routine ) ( void * ), void *arg );
```

- `thread`：指向`pthread_create`类型的指针，用于引用新创建的线程。
- `attr`：用于设置线程的属性，一般不需要特殊的属性，所以可以简单地设置为`NULL`。
- `(* start_routine)(void *)`：传递新线程所要执行的函数地址。
- `arg`：新线程所要执行的函数的参数。

&emsp;&emsp;`pthread_create`创建线程并使线程进入就绪态，而不是执行态。在进程执行到`sleep`函数时，它交出`cpu`控制权，于是线程得以执行。执行完线程后，这个线程也消失了。

#### 无参数的线程

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void *create ( void *arg ) {
    printf ( "New thread .... \n" );
    printf ( "This thread's id is %u\n", ( unsigned int ) pthread_self() );
    printf ( "The process pid is %d\n", getpid() );
    return ( void * ) 0;
}

int main ( int argc, char *argv[] ) {
    pthread_t tid;
    int error;
    printf ( "Main thread is starting ... \n" );
    error = pthread_create ( &tid, NULL, create, NULL );

    if ( error ) {
        printf ( "thread is not created ... \n" );
        return -1;
    }

    printf ( "The main process's pid is %d \n", getpid() );
    sleep ( 1 );
    return 0;
}
```

执行结果：

``` cpp
Main thread is starting ...
The main process's pid is 55
New thread ....
This thread's id is 1084225280
The process pid is 55
```

#### 使用简单参数的线程

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void *create ( void *arg ) {
    int *num;
    num = ( int * ) arg;
    printf ( "create parameter is %d \n", *num );
    return ( void * ) 0;
}

int main ( int argc, char *argv[] ) {
    pthread_t tidp;
    int error;
    int test = 4;
    int *attr = &test;
    error = pthread_create ( &tidp, NULL, create, ( void * ) attr );

    if ( error ) {
        printf ( "pthread_create is created is not created ... \n" );
        return -1;
    }

    sleep ( 1 );
    printf ( "pthread_create is created ...\n" );
    return 0;
}
```

执行结果：

``` cpp
create parameter is 4
pthread_create is created ...
```

#### 使用复杂参数的线程

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>
#include <stdlib.h>

struct menber {
    int a;
    char *s;
};

void *create ( void *arg ) {
    struct menber *temp;
    temp = ( struct menber * ) arg;
    printf ( "menber->a = %d \n", temp->a );
    printf ( "menber->s = %s \n", temp->s );
    return ( void * ) 0;
}

int main ( int argc, char *argv[] ) {
    pthread_t tidp;
    int error;
    struct menber *b;
    b = ( struct menber * ) malloc ( sizeof ( struct menber ) );
    b->a = 4;
    b->s = ( char * ) "zieckey";
    error = pthread_create ( &tidp, NULL, create, ( void * ) b );

    if ( error ) {
        printf ( "phread is not created ...\n" );
        return -1;
    }

    sleep ( 1 );
    printf ( "pthread is created...\n" );
    return 0;
}
```

执行结果：

``` cpp
menber->a = 4
menber->s = zieckey
pthread is created ...
```

### 线程等待

&emsp;&emsp;`pthread_join`函数原型如下，用于等待某个线程执行结束：

``` cpp
#include <pthread.h>
int pthread_join ( pthread_t th, void **thread_return );
```

- `th`：等待结束的线程，线程通过`pthread_create`返回的标识符来指定。
- `thread_return`：二级指针，它指向另一个指针，而后者指向线程的返回值。

调用成功完成后，`pthrea_join`返回`0`，其他任何返回值都表示出现了错误。

``` cpp
#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "pthread.h"
#include <unistd.h>

char message[32] = "Hello World";
void *thread_function ( void *arg );

int main ( void ) {
    pthread_t a_thread;
    void *thread_result;

    if ( pthread_create ( &a_thread, NULL, thread_function, ( void * ) message ) < 0 ) {
        perror ( "fail to pthread_create" );
        exit ( -1 );
    }

    printf ( "waiting for thread to finish\n" );

    if ( pthread_join ( a_thread, &thread_result ) < 0 ) {
        perror ( "fail to pthread_join" );
        exit ( -1 );
    }

    printf ( "MESSAGE is now %s\n", message );
    return 0;
}

void *thread_function ( void *arg ) {
    printf ( "thread_function is running, argument is %s\n", ( char * ) arg );
    strcpy ( message, "marked by thread" );
}
```

执行结果：

``` cpp
waiting for thread to finish
thread_function is running, argument is Hello World
MESSAGE is now marked by thread
```

可以看到，创建的新线程修改的数组`message`的值，而原先的线程也可以访问到，原因是线程之间则是共享进程的相关资源。

### 线程终止

&emsp;&emsp;如果进程中任何一个线程中调用`exit`或`_exit`，那么整个进程都会终止。
&emsp;&emsp;线程的正常退出方式如下：

- 线程从启动例程中返回。
- 线程可以被另一个进程终止。
- 线程自己调用`pthread_exit`函数。

&emsp;&emsp;`pthread_exit`函数原型如下：

``` cpp
#include <pthread.h>
void pthread_exit ( void *retval );
```

参数`retval`指向线程想要返回的某个对象。

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <pthread.h>

void *print_message_function ( void *ptr ) {
    char *message;
    message = ( char * ) ptr;
    char * return_message = ( char * ) "thread all done";
    printf ( "%s ", message );
    printf ( "PID: %ld \n", pthread_self() );
    pthread_exit ( return_message );
}

int main() {
    pthread_t thread1, thread2;
    char *message1 = ( char * ) "Thread 1";
    char *message2 = ( char * ) "Thread 2";
    int iret1, iret2;
    void *pth_join_ret1;
    void *pth_join_ret2;
    iret1 = pthread_create ( &thread1, NULL, print_message_function, ( void* ) message1 );
    iret2 = pthread_create ( &thread2, NULL, print_message_function, ( void* ) message2 );
    pthread_join ( thread1, &pth_join_ret1 );
    pthread_join ( thread2, &pth_join_ret2 );
    printf ( "Thread 1 returns: %d\n", iret1 );
    printf ( "Thread 2 returns: %d\n", iret2 );
    printf ( "pthread_join 1 returns: %s\n", ( char * ) pth_join_ret1 );
    printf ( "pthread_join 2 returns: %s\n", ( char * ) pth_join_ret2 );
    exit ( 0 );
}
```

执行结果：

``` cpp
Thread 1 PID: 140544497379072
Thread 2 PID: 140544488986368
Thread 1 returns: 0
Thread 2 returns: 0
pthread_join 1 returns: thread all done
pthread_join 2 returns: thread all done
```

### 线程释放

&emsp;&emsp;`linux`的线程有`2`种状态：

1. `joinable`状态：当线程函数自己返回退出，或调用`pthread_exit`时，都不会释放线程所占用堆栈和线程描述符。只有在调用`pthread_join`后，这些资源才会被释放。
2. `unjoinable`状态：线程资源在线程函数自己返回退出，或调用`pthread_exit`时，自动会被释放。

&emsp;&emsp;`unjoinable`状态的设定方法有`2`种：

1. 在使用`pthread_create`，在创建线程时指定。
2. 在线程中使用`pthread_detach ( pthread_self() )`：

``` cpp
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>

void* print_message_function ( void *ptr );

int main ( void ) {
    pthread_t thread1;

    while ( 1 ) {
        pthread_create ( &thread1, NULL, print_message_function, NULL );
        printf ( "%ld\n", thread1 );
        sleep ( 1 );
    }

    return ( 0 );
}

void* print_message_function ( void *ptr ) {
    pthread_detach ( pthread_self() );
    printf ( "This is thread\n" );
    pthread_exit ( 0 );
}
```