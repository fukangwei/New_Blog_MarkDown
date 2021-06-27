---
title: exec函数族
categories: Linux系统编程
date: 2019-02-02 21:34:35
---
&emsp;&emsp;`exec`启动一个新程序，替换原有的进程，而进程的`PID`不会被改变。<!--more-->

### execl

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execl ( const char *path, const char *arg1, ... );
```

- `path`是被执行程序名，包含完整路径。
- `arg1`至`argn`是被执行程序所需的命令行参数，以`NULL`结束。

``` cpp
#include "sys/types.h"
#include "sys/wait.h"
#include "unistd.h"
#include "stdio.h"
#include "stdlib.h"

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

- `path`是被执行程序名，不含路径，将从环境变量`PATH`中查找该程序。
- `arg1`至`argn`是被执行程序所需的命令行参数，以`NULL`结束。

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

- `path`是被执行程序名，含完整路径。
- `argv`是被执行程序所需的命令行参数数组。

``` cpp
#include <unistd.h>

int main() {
    char *argv[] = { ( char * ) "ls", ( char * ) "-al", ( char * ) "/etc/passwd", NULL};
    execv ( "/bin/ls", argv );
}
```

### execve

&emsp;&emsp;函数原型如下：

``` cpp
#include <unistd.h>
int execve ( const char *filename, char *const argv[], char *const envp[] );
```

`execve`常用于调用外部程序。
&emsp;&emsp;`new.c`如下：

``` cpp
#include "stdio.h"

int main ( void ) {
    printf ( "welcome to here!\n" );
    return 0;
}
```

&emsp;&emsp;`exec.c`如下：

``` cpp
#include "stdio.h"
#include "unistd.h"

extern char **environ;

int main ( int argc, char *argv[] ) {
    execve ( "./new", argv, environ );
}
```