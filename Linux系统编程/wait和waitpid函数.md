---
title: wait和waitpid函数
categories: Linux系统编程
date: 2019-02-03 15:08:47
---
### wait函数

&emsp;&emsp;`wait`函数用于等待子进程的退出：<!--more-->

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
pid_t wait ( int *status );
```

参数`status`用来保存被收集进程退出时的一些状态。如果不在意子进程的退出状态，可以设定这个参数为`NULL`。
&emsp;&emsp;如果执行成功，`wait`会返回子进程的`PID`；如果没有子进程，则`wait`返回`-1`。

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main ( void ) {
    pid_t pc, pr;
    pc = fork();

    if ( pc < 0 ) {
        printf ( "error ocurred!\n" );
    } else if ( pc == 0 ) { /* 如果是子进程 */
        printf ( "This is child process with pid of %d\n", getpid() );
        sleep ( 10 ); /* 睡眠10秒钟 */
    } else { /* 如果是父进程 */
        pr = wait ( NULL ); /* 在这里等待 */
        printf ( "I catched a child process with pid of %d\n", pr );
    }

    exit ( 0 );
}
```

执行结果：

``` cpp
This is child process with pid of 298
等待10秒
I catched a child process with pid of 298
```

&emsp;&emsp;如果参数`status`的值不是`NULL`，`wait`就会把子进程退出时的状态取出并存入其中。

1. `WIFEXITED(status)`：这个宏用来指出子进程是否为正常退出的，如果是，它会返回一个非零值。
2. `WEXITSTATUS(status)`：当`WIFEXITED`返回非零值时，我们可以用这个宏来提取子进程的返回值。

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main ( void ) {
    int status;
    pid_t pc, pr;
    pc = fork();

    if ( pc < 0 ) {
        printf ( "error ocurred!\n" );
    } else if ( pc == 0 ) { /* 子进程 */
        printf ( "This is child process with pid of %d\n", getpid() );
        exit ( 3 ); /* 子进程返回3 */
    } else { /* 父进程 */
        pr = wait ( &status );

        if ( WIFEXITED ( status ) ) { /* 如果WIFEXITED返回非零值 */
            printf ( "the child process %d exit normally\n", pr );
            printf ( "the return code is %d\n", WEXITSTATUS ( status ) );
        } else { /* 如果WIFEXITED返回零 */
            printf ( "the child process %d exit abnormally\n", pr );
        }
    }
}
```

编译并运行：

``` cpp
This is child process with pid of 308
the child process 308 exit normally
the return code is 3
```

### waitpid函数

&emsp;&emsp;`waitpid`系统调用在`Linux`函数库中的原型如下：

``` cpp
#include <sys/types.h>
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