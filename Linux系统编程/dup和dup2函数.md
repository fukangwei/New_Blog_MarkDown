---
title: dup和dup2函数
categories: Linux系统编程
abbrlink: a12dbcf8
date: 2019-02-03 00:13:39
---
&emsp;&emsp;这两个函数的作用都是复制一个现存的文件描述符：<!--more-->

``` cpp
#include <unistd.h>
int dup ( int oldfd );
int dup2 ( int oldfd, int newfd );
```

当调用`dup`函数时，内核在进程中创建一个新的文件描述符，此描述符是当前可用文件描述符的最小数值，这个文件描述符指向`oldfd`所拥有的文件表项。假如`oldfd`的值为`1`，当前文件描述符的最小值为`3`，那么新描述符`3`指向描述符`1`所拥有的文件表项。
&emsp;&emsp;`dup2`和`dup`的区别就是，它可以用`newfd`参数指定新描述符的数值，如果`newfd`已经打开，则先将其关闭。如果`newfd`等于`oldfd`，则`dup2`返回`newfd`，而不关闭它。`dup2`函数返回的新文件描述符同样与参数`oldfd`共享同一文件表项。`APUE`用另外一个种方法说明了这个问题：实际上，调用`dup ( oldfd );`等效于：

``` cpp
fcntl ( oldfd, F_DUPFD, 0 );
```

而调用`dup2 ( oldfd, newfd );`等效于：

``` cpp
close ( oldfd );
fcntl ( oldfd, F_DUPFD, newfd );
```

&emsp;&emsp;`dup`函数实例如下：

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>
​
int main ( int argc, char *argv[] ) {
    int fd = open ( "hello", O_CREAT | O_RDWR | O_TRUNC, S_IRUSR | S_IWUSR );
​
    if ( fd < 0 ) {
        printf ( "Open Error!!\n" );
        return 0;
    }
​
    int nfd = dup ( fd );
​
    if ( nfd < 0 ) {
        printf ( "Error!\n" );
        return 0;
    }
​
    char buf[1000];
    int n;
​
    while ( ( n = read ( STDIN_FILENO, buf, 1000 ) ) > 0 ) {
        if ( write ( nfd, buf, n ) != n ) {
            printf ( "Write Error!!\n" );
            return 0;
        }
    }
​
    return 0;
}
```

在上面代码中，`nfd`拷贝了`fd`，所以`write ( nfd, buf, n );`写到`nfd`所代表的文件时，也就是写到`fd`所代表的文件。程序执行完后，可以在相应目录的`hello`文件中看到输出：

``` bash
$ gcc dup.c
$ ls
a.out  dup.c
$ ./a.out
hello world
^C
$ ls
a.out  dup.c  hello
$ cat hello
hello world
```

&emsp;&emsp;`dup2`函数实例如下：

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>
​
int main ( int argc, char *argv[] ) {
    int fd = open ( "hello.file", O_CREAT | O_RDWR | O_TRUNC, S_IRUSR | S_IWUSR );
​
    if ( fd < 0 ) {
        printf ( "Open Error!!\n" );
        return 0;
    }
​
    int nfd = dup2 ( fd, STDOUT_FILENO );
​
    if ( nfd < 0 ) {
        printf ( "Error!\n" );
        return 0;
    }
​
    char buf[5];
    int n;
​
    while ( ( n = read ( STDIN_FILENO, buf, 5 ) ) > 0 )
        if ( write ( STDOUT_FILENO, buf, n ) != n ) {
            printf ( "Write Error!!\n" );
            return 0;
        }
​
    return 0;
}
```

上面的例子使用`dup2`将标准输出重定向为`hello.file`文件：

``` bash
$ ls
dup2.c
$ gcc dup2.c
$ ./a.out
hello world
^C
$ cat hello.file
hello world
```