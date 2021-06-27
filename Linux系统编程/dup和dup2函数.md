---
title: dup和dup2函数
categories: Linux系统编程
date: 2019-02-03 00:13:39
---
### 函数原型

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <unistd.h>
int dup ( int oldfd );
int dup2 ( int oldfd, int newfd );
```

当调用`dup`和`dup2`函数时，内核在进程中创建一个新的文件描述符，该文件描述符指向`oldfd`的文件表项。
&emsp;&emsp;`dup2`和`dup`的区别如下：

1. `dup2`可以用`newfd`指定新文件描述符的数值，如果`newfd`已经打开，则先将其关闭。
2. 如果`newfd`等于`oldfd`，则`dup2`返回`newfd`，而不关闭它。

`dup2`函数返回的新文件描述符同样与参数`oldfd`共享同一文件表项。

### dup函数实例

&emsp;&emsp;使用`dup`将标准输出重定向到文件`hello`：

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main ( int argc, char *argv[] ) {
    int fd = open ( "hello", O_CREAT | O_RDWR | O_TRUNC, S_IRUSR | S_IWUSR );

    if ( fd < 0 ) {
        printf ( "Open Error!!\n" );
        return 0;
    }

    int nfd = dup ( fd );

    if ( nfd < 0 ) {
        printf ( "Error!\n" );
        return 0;
    }

    char buf[1000];
    int n;

    while ( ( n = read ( STDIN_FILENO, buf, 1000 ) ) > 0 ) {
        if ( write ( nfd, buf, n ) != n ) {
            printf ( "Write Error!!\n" );
            return 0;
        }
    }

    return 0;
}
```

### dup2函数实例

&emsp;&emsp;使用`dup2`将标准输出重定向到文件`hello.file`：

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main ( int argc, char *argv[] ) {
    int fd = open ( "hello.file", O_CREAT | O_RDWR | O_TRUNC, S_IRUSR | S_IWUSR );

    if ( fd < 0 ) {
        printf ( "Open Error!!\n" );
        return 0;
    }

    int nfd = dup2 ( fd, STDOUT_FILENO );

    if ( nfd < 0 ) {
        printf ( "Error!\n" );
        return 0;
    }

    char buf[5];
    int n;

    while ( ( n = read ( STDIN_FILENO, buf, 5 ) ) > 0 )
        if ( write ( STDOUT_FILENO, buf, n ) != n ) {
            printf ( "Write Error!!\n" );
            return 0;
        }

    return 0;
}
```