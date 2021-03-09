---
title: wait和waitpid函数
categories: Linux系统编程
date: 2019-02-03 15:08:47
---
&emsp;&emsp;`wait`函数原型如下：<!--more-->

``` cpp
#include <sys/types.h> /* 提供类型pid_t的定义 */
#include <sys/wait.h>

pid_t wait ( int *status );
```

&emsp;&emsp;进程一旦调用了`wait`，就立即阻塞自己，由`wait`自动分析是否当前进程的某个子进程已经退出。如果让它找到了这样一个已经变成僵尸的子进程，`wait`就会收集这个子进程的信息，并把它彻底销毁后返回；如果没有找到这样一个子进程，`wait`就会一直阻塞在这里，直到有一个出现为止。
&emsp;&emsp;参数`status`用来保存被收集进程退出时的一些状态，它是一个指向`int`类型的指针。但如果我们对这个子进程是如何死掉的毫不在意，只想把这个僵尸进程消灭掉(事实上绝大多数情况下，我们都会这样想)，我们就可以设定这个参数为`NULL`：

``` cpp
pid = wait ( NULL );
```

如果成功，`wait`会返回被收集的子进程的进程`ID`。如果调用进程没有子进程，调用就会失败，此时`wait`返回`-1`，同时`errno`被置为`ECHILD`。`wait`调用例程如下：

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdlib.h>

int main ( void ) {
    pid_t pc, pr;
    pc = fork();

    if ( pc < 0 ) { /* 如果出错 */
        printf ( "error ocurred!/n" );
    } else if ( pc == 0 ) { /* 如果是子进程 */
        printf ( "This is child process with pid of %d/n", getpid() );
        sleep ( 10 ); /* 睡眠10秒钟 */
    } else { /* 如果是父进程 */
        pr = wait ( NULL ); /* 在这里等待 */
        printf ( "I catched a child process with pid of %d/n" ), pr );
    }

    exit ( 0 );
}
```

编译并运行：

``` cpp
$ gcc wait1.c -o wait1
$ ./wait1
This is child process with pid of 1508
I catched a child process with pid of 1508
```

&emsp;&emsp;可以明显注意到，在第`2`行结果打印出来前有`10`秒钟的等待时间，这就是我们设定的让子进程睡眠的时间。
&emsp;&emsp;如果参数`status`的值不是`NULL`，`wait`就会把子进程退出时的状态取出并存入其中，这是一个整数值(`int`)，指出了子进程是正常退出还是被非正常结束的(一个进程也可以被其他进程用信号结束)，以及正常结束时的返回值，或是被哪一个信号结束的等信息。由于这些信息被存放在一个整数的不同二进制位中，所以用常规的方法读取会非常麻烦，人们就设计了一套专门的宏(`macro`)来完成这项工作，下面我们来学习一下其中最常用的两个：

- `WIFEXITED(status)`：这个宏用来指出子进程是否为正常退出的，如果是，它会返回一个非零值(请注意，虽然名字一样，这里的参数`status`并不同于`wait`唯一的参数，即指向整数的指针`status`，而是那个指针所指向的整数，切记不要搞混了)。
- `WEXITSTATUS(status)`：当`WIFEXITED`返回非零值时，我们可以用这个宏来提取子进程的返回值。如果子进程调用`exit(5)`退出，`WEXITSTATUS(status)`就会返回`5`。请注意，如果进程不是正常退出的，也就是说`WIFEXITED`返回`0`，这个值就毫无意义。

&emsp;&emsp;下面通过例子来实战一下我们刚刚学到的内容：

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main ( void ) {
    int status;
    pid_t pc, pr;
    pc = fork();

    if ( pc < 0 ) { /* 如果出错 */
        printf ( "error ocurred!/n" );
    } else if ( pc == 0 ) { /* 子进程 */
        printf ( "This is child process with pid of %d./n", getpid() );
        exit ( 3 ); /* 子进程返回3 */
    } else { /* 父进程 */
        pr = wait ( &status );

        if ( WIFEXITED ( status ) ) { /* 如果WIFEXITED返回非零值 */
            printf ( "the child process %d exit normally./n", pr );
            printf ( "the return code is %d./n", WEXITSTATUS ( status ) );
        } else { /* 如果WIFEXITED返回零 */
            printf ( "the child process %d exit abnormally./n", pr );
        }
    }
}
```

编译并运行：

``` cpp
$ gcc wait2.c -o wait2
$ ./wait2
This is child process with pid of 1538.
the child process 1538 exit normally.
the return code is 3.
```

父进程准确捕捉到了子进程的返回值`3`，并把它打印了出来。
&emsp;&emsp;有时候，父进程要求子进程的运算结果进行下一步的运算，或者子进程的功能是为父进程提供了下一步执行的先决条件(例如子进程建立文件，而父进程写入数据)，此时父进程就必须在某一个位置停下来，等待子进程运行结束。而如果父进程不等待而直接执行下去的话，会出现极大的混乱。这种情况称为`进程之间的同步`，更准确地说，这是进程同步的一种特例。进程同步就是要协调好`2`个以上的进程，使之以安排好地次序依次执行。解决进程同步问题有更通用的方法，但对于我们假设的这种情况，则完全可以用`wait`系统调用简单的予以解决。请看下面这段程序：

``` cpp
#include <sys/types.h>
#include <sys/wait.h>

int main ( void ) {
    pid_t pc, pr;
    int status;
    pc = fork();

    if ( pc < 0 ) {
        printf ( "Error occured on forking./n" );
    } else if ( pc == 0 ) { /* 子进程的工作 */
        exit ( 0 );
    } else { /* 父进程的工作 */
        pr = wait ( &status );
        /* 利用子进程的结果 */
    }
}
```

这段程序说明了一些问题：当`fork`调用成功后，父子进程各做各的事情，但当父进程的工作告一段落，需要用到子进程的结果时，它就停下来调用`wait`，一直等到子进程运行结束，然后利用子进程的结果继续执行，这样就圆满地解决了我们提出的进程同步问题。
&emsp;&emsp;`waitpid`系统调用在`Linux`函数库中的原型如下：

``` cpp
#include <sys/types.h> /* 提供类型pid_t的定义 */
#include <sys/wait.h>

pid_t waitpid ( pid_t pid, int *status, int options );
```

从本质上讲，系统调用`waitpid`和`wait`的作用是完全相同的，但`waitpid`多出了两个可由用户控制的参数`pid`和`options`，从而为我们编程提供了另一种更灵活的方式。

- `pid`：它是一个进程`ID`，但当`pid`取不同的值时，在这里有不同的意义：

取值       | 意义
-----------|---
`pid > 0`  | 只等待进程`ID`等于`pid`的子进程，不管已经有多少其他子进程运行结束退出了，只要指定的子进程还没有结束，`waitpid`就会一直等下去
`pid = -1` | 等待任何一个子进程退出，没有任何限制，此时`waitpid`和`wait`的作用一模一样
`pid = 0`  | 等待同一个进程组中的任何子进程，如果子进程已经加入了别的进程组，`waitpid`不会对它做任何理睬
`pid < -1` | 等待一个指定进程组中的任何子进程，这个进程组的`ID`等于`pid`的绝对值

- `options`：`options`提供了一些额外的选项来控制`waitpid`，目前`Linux`只支持`WNOHANG`和`WUNTRACED`两个选项，这是两个常数，可以用`|`运算符把它们连接起来：

``` cpp
ret = waitpid ( -1, NULL, WNOHANG | WUNTRACED );
```

如果不想使用它们，也可以把`options`设为`0`：

``` cpp
ret = waitpid ( -1, NULL, 0 );
```

如果使用了`WNOHANG`参数调用`waitpid`，即使没有子进程退出，它也会立即返回，不会像`wait`那样永远等下去。而`WUNTRACED`由于涉及到一些跟踪调试方面的知识，极少用到。
&emsp;&emsp;实际上`wait`就是经过包装的`waitpid`，查看`<内核源码目录>/include/unistd.h`，就会发现以下程序段：

``` cpp
static inline pid_t wait ( int *wait_stat ) {
    return waitpid ( -1, wait_stat, 0 );
}
```

&emsp;&emsp;`waitpid`的返回值比`wait`稍微复杂一些，一共有如下几种情况：

- 当正常返回的时候，`waitpid`返回收集到的子进程的进程`ID`。
- 如果设置了选项`WNOHANG`，而调用中`waitpid`发现没有已退出的子进程可收集，则返回`0`。
- 如果调用中出错，则返回`-1`，这时`errno`会被设置成相应的值以指示错误所在。
- 当`pid`所指示的子进程不存在，或此进程存在，但不是调用进程的子进程，`waitpid`就会出错返回，这时`errno`被设置为`ECHILD`。

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main ( void ) {
    pid_t pc, pr;
    pc = fork();

    if ( pc < 0 ) { /* 如果fork出错 */
        printf ( "Error occured on forking./n" );
    } else if ( pc == 0 ) { /* 如果是子进程 */
        sleep ( 10 ); /* 睡眠10秒 */
        exit ( 0 );
    }

    /* 如果是父进程 */
    do {
        pr = waitpid ( pc, NULL, WNOHANG ); /* 使用了WNOHANG参数，waitpid不会在这里等待 */

        if ( pr == 0 ) { /* 如果没有收集到子进程 */
            printf ( "No child exited/n" );
            sleep ( 1 );
        }
    } while ( pr == 0 ); /* 没有收集到子进程，就回去继续尝试 */

    if ( pr == pc ) {
        printf ( "successfully get child %d/n", pr );
    } else {
        printf ( "some error occured/n" );
    }
}
```

编译并运行：

``` cpp
$ cc waitpid.c -o waitpid
$ ./waitpid
No child exited
No child exited
No child exited
No child exited
No child exited
No child exited
No child exited
No child exited
No child exited
No child exited
successfully get child 1526
```

父进程经过`10`次失败的尝试之后，终于收集到了退出的子进程。