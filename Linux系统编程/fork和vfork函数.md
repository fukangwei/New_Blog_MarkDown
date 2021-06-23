---
title: fork和vfork函数
categories: Linux系统编程
date: 2019-03-16 15:40:43
---
### fork

&emsp;&emsp;`fork`函数的功能是创建子进程：<!--more-->

``` cpp
#include <unistd.h>
pid_t fork ( void );
```

&emsp;&emsp;`fork`的奇妙之处在于：它被调用一次，却返回两次。它可能有`3`种不同的返回值：

- 在父进程中，`fork`返回新创建的子进程的`PID`。
- 在子进程中，`fork`返回`0`。
- 如果出现错误，`fork`返回一个负值。

#### fork实例1

&emsp;&emsp;代码实例：

``` cpp
#include "unistd.h"
#include "stdio.h"
#include "stdlib.h"

int main() {
    pid_t pid;
    pid = fork();

    if ( pid > 0 ) {
        printf ( "This is father process\n" );
        exit ( 0 );
    } else {
        printf ( "This is child process\n" );
        exit ( 0 );
    }
}
```

执行结果：

``` cpp
This is father process
This is child process
```

&emsp;&emsp;在`pid = fork();`之前，只有一个进程在执行，但在这条语句执行之后，就变成两个进程在执行了。
&emsp;&emsp;这两个进程共享代码段，将要执行的下一条语句都是`if ( pid > 0 ) {}`。
&emsp;&emsp;原来就存在的进程被称作`父进程`，新出现的进程被称作`子进程`，父子进程的区别在于`PID`不同。

#### fork实例2

&emsp;&emsp;代码实例：

``` cpp
#include "unistd.h"
#include "stdio.h"
#include "stdlib.h"

int main() {
    pid_t pid;
    int count = 0;
    pid = fork();
    count++;
    printf ( "count is %d\n", count );
    exit ( 0 );
}
```

执行结果：

``` cpp
count = 1
count = 1
```

&emsp;&emsp;对`fork`函数，子进程的数据空间、堆栈空间都会从父进程得到一个拷贝，而不是共享。
&emsp;&emsp;在子进程中，对`count`进行加`1`的操作，并没有影响到父进程中的`count`，父进程中的`count`仍然为`0`。

### vfork

&emsp;&emsp;`vfork`函数的功能是创建子进程：

``` cpp
#include <sys/types.h>
#include <unistd.h>
pid_t vfork ( void );
```

&emsp;&emsp;`vfork`和`fork`的区别如下：

- `fork`：子进程拷贝父进程的数据段，父、子进程的执行次序不确定。
- `vfork`：子进程与父进程共享数据段，子进程先运行，父进程后运行。

#### vfork实例1

&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main ( void ) {
    pid_t pid;
    int var = 88;

    if ( ( pid = vfork() ) < 0 ) {
        printf ( "vfork error" );
        exit ( -1 );
    } else if ( pid == 0 ) { /* 子进程 */
        var++;
        exit ( 0 );
    }

    printf ( "pid = %d, var = %d\n", getpid(), var );
    return 0;
}
```

执行结果：

``` cpp
pid = 283, var = 89
```

注意，从子进程中退出必须使用`exit`函数，不能使用`return`函数。

#### vfork实例2

&emsp;&emsp;代码实例：

``` cpp
#include "unistd.h"
#include "stdio.h"
#include "stdlib.h"

int main() {
    pid_t pid;
    int count = 0;
    pid = vfork();
    count++;
    printf ( "count is %d\n", count );
    exit ( 0 );
}
```

执行结果：

``` cpp
count is 1
count is 2
```