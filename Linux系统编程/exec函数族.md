---
title: exec函数族
categories: Linux系统编程
date: 2019-02-02 21:34:35
---
&emsp;&emsp;`exec`用被执行的程序替换调用它的程序。区别为`fork`创建一个新的进程，产生一个新的`PID`；`exec`启动一个新程序，替换原有的进程，因此进程的`PID`不会改变。<!--more-->

### execl

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execl ( const char *path, const char *arg1, ... );
```

参数`path`是被执行程序名(含完整路径)；`arg1`至`argn`是被执行程序所需的命令行参数，含程序名，以空指针`NULL`结束。

``` cpp
#include "sys/types.h"
#include "unistd.h"
#include "stdio.h"
#include "sys/wait.h"

int main() {
    pid_t pid;
    pid = vfork();

    if ( pid > 0 ) {
        wait ( NULL );
        printf ( "This is father process\n" );
        exit ( 0 );
    } else {
        execl ( "/bin/ls", "ls", "-al", "./", NULL );
        printf ( "This is child process\n" );
        exit ( 0 );
    }
}
```

### execlp

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execlp ( const char *path, const char *arg1, ... );
```

参数`path`是被执行程序名(不含路径，将从`path`环境变量中查找该程序)；`arg1`至`argn`是被执行程序所需的命令行参数，含程序名，以空指针`NULL`结束。

``` cpp
#include <unistd.h>

int main() {
    execlp ( "ls", "ls", "-al", "/etc/passwd", ( char * ) 0 );
}
```

### execv

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execv ( const char *path, char *const argv[] );
```

参数`path`是被执行程序名(含完整路径)；`argv`是被执行程序所需的命令行参数数组。

``` cpp
#include <unistd.h>

int main() {
    char *argv[] = {"ls", "-al", "/etc/passwd", ( char * ) 0};
    execv ( "/bin/ls", argv );
}
```

### execve

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execve ( const char *filename, char *const argv[], char *const envp[] );
```

- `filename`：必须是一个二进制的可执行文件，或者是一个脚本以`#!`格式开头的解释器参数参数。如果是后者，这个解释器必须是一个可执行的有效的路径名，但是不是脚本本身，它将调用解释器作为文件名。
- `argv`：它是要调用的程序执行的参数序列，也就是我们要调用的程序需要传入的参数。
- `envp`：同样也是参数序列，一般来说是一种键值对的形式`key = value`，作为新程序的环境。

注意，`argv`和`envp`都必须以`null`指针结束。

``` cpp
#include <stdio.h>
#include <unistd.h>

int main ( int arg, char **args ) {
    char *argv[] = {"ls", "-al", "/home/chicho/result/", NULL};
    char *envp[] = {0, NULL}; /* 传递给执行文件新的环境变量数组 */
    execve ( "/bin/ls", argv, envp );
}
```

### execvp

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execvp ( const char *file, char *const argv[] );
```

`execvp`函数会从`PATH`环境变量所指的目录中查找文件名为第一个参数指示的字符串，找到后执行该文件，第二个及以后的参数代表执行文件时传递的参数列表，最后一个成员必须是空指针。

``` cpp
#include <unistd.h>

int main() {
    char *argv[] = {"ls", "-l", "/etc", ( char * ) 0};
    execvp ( "ls", argv );
    return 0;
}
```

### exec调用外部程序

&emsp;&emsp;`exec.c`如下：

``` cpp
#include "stdio.h"
#include "unistd.h"
#include "sys/types.h"

extern char **environ;

int main ( int argc, char *argv[] ) {
    execve ( "./new", argv, environ );
}
```

&emsp;&emsp;`new.c`如下：

``` cpp
#include "stdio.h"
#include "unistd.h"
#include "sys/types.h"

int main ( void ) {
    puts ( "welcome to here!\n" );
    return 0;
}
```