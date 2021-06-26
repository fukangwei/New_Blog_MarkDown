---
title: 信号量_POSIX
date: 2021-06-27 11:33:30
categories: Linux系统编程
---
### 创建信号量

&emsp;&emsp;使用`sem_init`创建信号量：<!--more-->

``` cpp
#include <semaphore.h>
int sem_init ( sem_t *sem, int pshared, unsigned int value );
```

- `sem`：信号量对象。
- `pshared`：通常设为`0`。
- `value`：信号量的初始值。

### 控制信号量

&emsp;&emsp;使用`sem_wait`和`sem_post`控制信号量：

``` cpp
#include <semaphore.h>
int sem_wait ( sem_t *sem );
int sem_post ( sem_t *sem );
```

- `sem_post`的作用是给信号量的值加`1`。
- `sem_wait`的作用是给信号量的值减`1`：

1. `sem_wait`会等到信号量`非0`时，才会开始减法操作。
2. 如果信号量为`0`，`sem_wait`就会等待，直到有线程增加了该信号量的值。

### 销毁信号量

&emsp;&emsp;使用`sem_destory`销毁信号量：

``` cpp
#include <semaphore.h>
int sem_destory ( sem_t *sem );
```

### 代码实例

&emsp;&emsp;代码实例：

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
        printf ( "You input %ld characters\n", strlen ( buffer ) );
        sem_wait ( &sem );
    }

    pthread_exit ( NULL );
}
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