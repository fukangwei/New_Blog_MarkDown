---
title: stat、fstat和lstat函数
date: 2019-02-03 16:33:46
categories: Linux系统编程
---
&emsp;&emsp;使用函数`stat`、`fstat`和`lstat`来检查文件的属性，这些函数使用`struct stat`对象来返回属性信息。所有这三个函数声明在头文件`sys/stat.h`。
&emsp;&emsp;`stat`函数原型如下：

``` cpp
int stat ( const char *filename, struct stat *buf );
```

`stat`函数返回`buf`指向的结构体中`filename`项中保存的文件名的文件的属性信息。如果`filename`项是一个符号链接，你得到的是链接指向的文件的属性信息。如果链接指向一个不存在的文件，则`stat`函数会报告一个文件不存在的失败消息。如果操作成功的话，该函数返回值为`0`，失败则返回`-1`。
&emsp;&emsp;`fstat`函数原型如下：

``` cpp
int fstat ( int filedes, struct stat *buf );
```

`fstat`函数与`stat`函数类似，只是在打开一个文件时使用文件描述符代替文件名做为参数。像`stat`函数一样，`fstat`函数调用成功返回值为`0`，失败则返回`-1`。
&emsp;&emsp;`lstat`函数原型如下：

``` cpp
int lstat ( const char *filename, struct stat *buf );
```

`lstat`函数与`stat`函数类似，区别在于`lstat`函数不对符号链接进行追踪。如果参数`filename`是一个符号链接名，`lstat`函数仅仅返回链接本身的信息，否则`lstat`函数与`stat`函数作用相同。
&emsp;&emsp;下面是`struct stat`内各参数的说明：

``` cpp
struct stat {
    mode st_mode;    /* 文件类型和文件权限   */
    ino st_ino;      /* inode节点号         */
    dev st_dev;      /* 设备号(文件系统)     */
    dev st_rdev;     /* 特殊文件的设备号     */
    nlink st_nlink;  /* 硬连接数            */
    uid st_uid;      /* 属主的用户ID        */
    gid st_gid;      /* 属主的组ID          */
    off st_size;     /* 普通文件的字节长度   */
    time st_atime;   /* 最后存取时间        */
    time st_mtime;   /* 最后修改存取时间     */
    time st_ctime;   /* 最后文件状态更改时间 */
    long st_blksize; /* 最佳I/O块长         */
    long st_blocks;  /* 分配的512字节块块数  */
};
```

范例如下：

``` cpp
#include <sys/stat.h>
#include <unistd.h>
#include "stdio.h"
​
int main() {
    struct stat buf;
    stat ( "/etc/passwd", &buf );
    printf ( "/etc/passwd file size = %ld\n", buf.st_size );
}
```

&emsp;&emsp;`Unix`系统的大多数文件是普通文件或目录，但是也有另外一些文件类型：

- `普通文件`(`Regular file`)：这是最常见的文件类型，这种文件包含了某种形式的数据。至于这种数据是文本还是二进制数据，对于系统核而言并无区别。对普通文件内容的解释由处理该文件的应用程序进行。
- `目录文件`(`Directory file`)：这种文件包含了其它文件的名字以及指向与这些文件有关信息的指针。对一个目录文件具有读许可数的任一进程都可以读该目录的内容，但只有系统内核可以写目录文件。
- `字符特殊文件`(`Charocter special file`)：这种文件用于系统中的某些类型的设备。
- `块特殊文件`(`Block special file`)：这种文件典型地用于磁盘设备。系统中的所有设备或者是字符特殊文件，或者是块特殊文件。
- `FIFO`：这种文件用于进程间的通信，有时也将其称为命名管道。
- `套接口`(`socket`)：这种文件用于进程间的网络通信，也可用于在一台宿主机上的进程之间的非网络通信。
- `符号连接`(`Symboliclink`)：这种文件指向另一个文件。

&emsp;&emsp;`Linux`提供了一些特殊的宏给用户进程使用以判断文件的类型，由于`Linux`中有`7`种类型的文件，所以对应有`7`种判断文件类型的宏：

宏                         | 说明
---------------------------|-----
`S_ISREG ( buf.st_mode )`  | 普通文件
`S_ISDIR ( buf.st_mode )`  | 目录文件
`S_ISBLK ( buf.st_mode )`  | 块设备文件
`S_ISCHR ( buf.st_mode )`  | 字符设备文件
`S_ISLNK ( buf.st_mode )`  | 符号链接文件
`S_ISFIFO ( buf.st_mode )` | 有名管道文件
`S_ISSOCK ( buf.st_mode )` | 套接字文件

&emsp;&emsp;`Linux`中还提供了两个用来表示`set-user-id`和`set-group-id`两位(权限修饰位)的宏，这两位主要会影响到进程的有效用户`ID`和进程实际用户`ID`。

宏        | 说明
----------|-----
`S_ISUID` | 设置了`set-user-id`位
`S_ISGID` | 设置了`set-group-id`位

&emsp;&emsp;`Linux`提供了`9`个表示所有者、同组用户以及其他用户的权限的宏：

宏        | 说明
----------|-----
`S_IRUSR` | 用户读权限
`S_IWUSR` | 用户写权限
`S_IXUSR` | 用户执行权限
`S_IRGRP` | 同组用户读权限
`S_IWGRP` | 同组用户写权限
`S_IXGRP` | 同组用户执行权限
`S_IROTH` | 其他用户读权限
`S_IWOTH` | 其他用户写权限
`S_IXOTH` | 其他用户执行权限

&emsp;&emsp;打印文件的类型：

``` cpp
#include "apue.h"
#include "stdio.h"

int main ( int argc, char *argv[] ) {
    int i;
    struct stat buf;
    char *ptr;

    for ( i = 1; i < argc; i++ ) {
        printf ( "%s: ", argv[i] );

        if ( lstat ( argv[i], &buf ) < 0 ) {
            err_ret ( "lstat error" );
            continue;
        }

        if ( S_ISREG ( buf.st_mode ) ) {
            ptr = "regular";
        } else if ( S_ISDIR ( buf.st_mode ) ) {
            ptr = "directory";
        } else if ( S_ISCHR ( buf.st_mode ) ) {
            ptr = "character special";
        } else if ( S_ISBLK ( buf.st_mode ) ) {
            ptr = "block special";
        } else if ( S_ISFIFO ( buf.st_mode ) ) {
            ptr = "fifo";
        } else if ( S_ISLNK ( buf.st_mode ) ) {
            ptr = "symbolic link";
        } else if ( S_ISSOCK ( buf.st_mode ) ) {
            ptr = "socket";
        } else {
            ptr = "** unknown mode **";
        }

        printf ( "%s\n", ptr );
    }

    exit ( 0 );
}
```