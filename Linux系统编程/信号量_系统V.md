---
title: 信号量_系统V
categories: Linux系统编程
date: 2019-03-14 08:33:40
---
### 信号量概念

&emsp;&emsp;信号量可以让一个临界区同一时间只有一个线程在访问它，也就是说信号量是用来调协进程对共享资源的访问。<!--more-->
&emsp;&emsp;最简单的信号量是只能取`0`和`1`的变量，叫做`二进制信号量`，而可以取多个正整数的信号量被称为`通用信号量`。

### 工作原理

&emsp;&emsp;信号量只能进行两种操作，即申请信号量`P(sv)`和释放信号量`V(sv)`：

- `P(sv)`：如果`sv`的值大于零，就给它减`1`；如果它的值为`0`，就挂起该进程的执行。
- `V(sv)`：如果有其他进程因等待`sv`而被挂起，就让它恢复运行；如果没有进程因等待`sv`而挂起，就给它加`1`。

&emsp;&emsp;假如有两个进程共享信号量`sv`：

1. 其中一个进程执行了`P(sv)`，它将得到信号量，并使`sv`减`1`，然后就可以进入临界区。
2. 第二个进程将被阻止进入临界区，因为当它试图执行`P(sv)`时，`sv`为`0`，它会被挂起。
3. 第一个进程离开临界区域，执行`V(sv)`释放信号量，这时第二个进程就可以继续执行。

### semget

&emsp;&emsp;该函数创建一个新的信号量集，或者获取一个已经存在的信号量集：

``` cpp
#include <sys/sem.h>
int semget ( key_t key, int num_sems, int sem_flags );
```

- `key`：一个键值，用来标识一个全局唯一的信号量集，就像文件名全局唯一的标识一个文件一样。要通过信号量通信的进程需要使用相同的键值来创建或获取该信号量。
- `num_sems`：指定要创建或获取的信号量集内部信号量的数目。如果是创建信号量，则该值必须指定；如果是获取已存在的信号量，则可以设置为`0`。
- `sem_flags`：指定一组标志，其格式和含义与`open`的`mode`相同。

1. 可以和`IPC_CREAT`标志做按位`或`运算，此时即使信号量存在，`semget`也不会报错。
2. 可以用`IPC_CREAT | IPC_EXCL`来确保创建一组新的、唯一的信号量集，此时若信号量存在，`semget`则返回错误。

&emsp;&emsp;如果函数执行成功，则返回一个正整数，它是信号量集的标识符；失败时则返回`-1`。

### semop

&emsp;&emsp;该函数改变信号量的值，即执行`P`和`V`操作：

``` cpp
#include <sys/sem.h>
int semop ( int sem_id, struct sembuf *sem_ops, size_t num_sem_ops );
```

- `sem_id`：`semget`返回的信号量集标识符。
- `num_sem_ops`：指定要执行的操作个数，即`sem_ops`数组中元素的个数。
- `sem_ops`：指向一个`sembuf`结构体的数组：

``` cpp
struct sembuf {
    unsigned short int sem_num;
    short int sem_op;
    short int sem_flg;
};
```

内部成员的说明如下：

- `sem_num`是信号量集内部信号量的编号，像数组一样，从`0`开始。
- `sem_op`是信号量在一次操作中需要改变的数据，通常是两个数：

1. 一个是`-1`，即`P`操作。
2. 一个是`+1`，即`V`操作。

- `sem_flg`的可选值是`IPC_NOWAIT`、`SEM_UNDO`。

1. `IPC_NOWAIT`指无论信号量操作是否成功，`semop`调用都将立即返回。
2. `SEM_UNDO`指当进程退出时，取消正在进行的`semop`操作。

&emsp;&emsp;如果函数执行成功，则返回`0`，失败则返回`-1`。

### semctl

&emsp;&emsp;该函数允许调用者对信号量进行直接控制：

``` cpp
#include <sys/sem.h>
int semctl ( int sem_id, int sem_num, int command, ... );
```

- `sem_id`：`semget`返回的信号量集标识符。
- `sem_num`：指定被操作的信号量在信号量集中的编号。
- `command`：指定要执行的命令，有如下选项：

1. `SETVAL`：初始化信号量集。
2. `IPC_RMID`：删除信号量集标识符。

&emsp;&emsp;有的命令需要第`4`个参数，由用户自己定义，但`sys/sem.h`给出了推荐格式：

``` cpp
union semun {
    int val;
    struct semid_ds *buf;
    unsigned short *arry;
};
```

### IPC_PRIVATE

&emsp;&emsp;`semget`可以给参数`key`传递一个特殊的键值`IPC_PRIVATE`，这样无论该信号量是否存在，`semget`都将创建一个新的信号量。

### 信号量操作

&emsp;&emsp;代码实例：

``` cpp
#include <sys/sem.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

union semun {
    int val;
    struct semid_ds *buf;
    unsigned short int *array;
    struct seminfo *__buf;
};

/* op为“-1”时执行P操作，op为1时执行V操作 */
void pv ( int sem_id, int op ) {
    struct sembuf sem_b;
    sem_b.sem_num = 0;
    sem_b.sem_op = op;
    sem_b.sem_flg = SEM_UNDO;
    semop ( sem_id, &sem_b, 1 );
}

int main ( int argc, char *argv[] ) {
    int sem_id = semget ( IPC_PRIVATE, 1, 0666 );
    union semun sem_un;
    sem_un.val = 1;
    semctl ( sem_id, 0, SETVAL, sem_un );
    pid_t id = fork();

    if ( id < 0 ) {
        fprintf ( stderr, "fork failed.\n" );
        return 1;
    } else if ( id == 0 ) {
        printf ( "child try to get binary sem\n" );
        pv ( sem_id, -1 );
        printf ( "child get the sem and would release it after 5 seconds\n" );
        sleep ( 5 );
        pv ( sem_id, 1 );
        exit ( 0 );
    } else {
        printf ( "parent try to get binary sem\n" );
        pv ( sem_id, -1 );
        printf ( "parent get the sem and would release it after 3 seconds\n" );
        sleep ( 3 );
        pv ( sem_id, 1 );
    }

    waitpid ( id, NULL, 0 );
    semctl ( sem_id, 0, IPC_RMID, sem_un );
    return 0;
}
```