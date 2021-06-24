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

`status`用来保存子进程退出时的一些状态。如果不在意子进程的退出状态，可以设定`status`为`NULL`。
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

&emsp;&emsp;如果参数`status`的值不是`NULL`，`wait`就会把子进程退出时的状态取出，并存入其中。
&emsp;&emsp;可以使用以下宏来处理`status`：

1. `WIFEXITED(status)`：用来指出子进程是否为正常退出，如果是，则会返回一个非零值。
2. `WEXITSTATUS(status)`：当`WIFEXITED`返回非零值时，可以用这个宏来提取子进程的返回值。

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

&emsp;&emsp;`waitpid`的函数原型如下：

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
pid_t waitpid ( pid_t pid, int *status, int options );
```

从本质上讲，`waitpid`和`wait`的作用是完全相同的，但`waitpid`多出了两个可以由用户控制的参数`pid`和`options`：

- `pid`：当`pid`取不同的值时，在这里有不同的意义：

取值       | 意义
-----------|---
`pid > 0`  | 只等待进程`ID`等于`pid`的子进程
`pid = -1` | 等待任何一个子进程退出，此时`waitpid`和`wait`的作用一模一样
`pid = 0`  | 等待同一个进程组中的任何子进程
`pid < -1` | 等待一个指定进程组中的任何子进程，这个进程组的`ID`等于`pid`的绝对值

- `options`：最常用的选项是`WNOHANG`，作用是即使没有子进程退出，它也会立即返回。

&emsp;&emsp;`waitpid`的返回值有如下几种情况：

- 当正常返回时，`waitpid`返回子进程的`PID`。
- 如果设置了`WNOHANG`，而`waitpid`没有发现已经退出的子进程，则返回`0`。
- 如果`waitpid`出错，则返回`-1`。例如参数`pid`指示的子进程不存在，或此进程存在，但不是调用进程的子进程。

``` cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main ( void ) {
    pid_t pc, pr;
    pc = fork();

    if ( pc < 0 ) {
        printf ( "Error occured on forking\n" );
    } else if ( pc == 0 ) { /* 如果是子进程 */
        sleep ( 3 ); /* 睡眠3秒 */
        exit ( 0 );
    }

    do { /* 如果是父进程 */
        pr = waitpid ( pc, NULL, WNOHANG ); /* 使用了WNOHANG参数，waitpid不会在这里等待 */

        if ( pr == 0 ) { /* 如果没有收集到子进程 */
            printf ( "No child exited\n" );
            sleep ( 1 );
        }
    } while ( pr == 0 ); /* 没有收集到子进程，就回去继续尝试 */

    if ( pr == pc ) {
        printf ( "successfully get child %d\n", pr );
    } else {
        printf ( "some error occured\n" );
    }
}
```

执行结果：

``` cpp
No child exited
No child exited
No child exited
successfully get child 340
```