---
title: ferror和perror函数
categories: C语言语法详解
date: 2018-12-13 11:30:24
---
### ferror函数

&emsp;&emsp;`ferror`函数原型如下：<!--more-->

``` cpp
int ferror ( FILE* fp );
```

其功能是测试文件是否出现错误。返回值为未出错是`0`，出错为`非0值`。
&emsp;&emsp;在调用各种输入输出函数(例如`putc`、`getc`、`fread`和`fwrite`等)时，如果出现错误，除了函数返回值有所反映外，还可以用`ferror`函数检查。对同一个文件(文件指针或文件描述符)，每一次调用输入输出函数，均产生一个新的`ferror`函数值，因此应当在调用输入输出函数后立即检查`ferror`函数的值，否则信息会丢失。`fopen`打开文件时，`ferror`函数处置自动置为`0`。

``` cpp
#include <stdio.h>
#include <stdlib.h>

int main ( void ) {
    FILE* pf = fopen ( "C:\\test.txt", "r" );

    if ( pf == NULL ) {
        printf ( "\nopen fail" );
    } else {
        printf ( "\nopen sucess" );
    }

    if ( ferror ( pf ) == 0 ) {
        printf ( "\n正常" );
    } else {
        printf ( "\n异常" );
    }

    fputs ( "1234", pf );

    if ( ferror ( pf ) == 0 ) {
        printf ( "\n正常" );
    } else {
        printf ( "\n异常" );
    }
}
```

### perror函数

&emsp;&emsp;`perror`函数原型如下：

``` cpp
void perror ( const char* s );
```

`perror`用来将上一个函数发生错误的原因输出到标准设备(`stderr`)。参数`s`所指的字符串会先打印出，后面再加上错误原因字符串。此错误原因依照全局变量`error`的值来决定要输出的字符串。在库函数中有个`error`变量，每个`error`值对应着以字符串表示的错误类型。当你调用某些函数出错时，该函数已经重新设置了`error`的值。`perror`函数只是将你输入的一些信息和现在的`error`所对应的错误一起输出。

``` cpp
#include <stdio.h>

int main ( void ) {
    FILE* fp;
    fp = fopen ( "/root/no_exit_file", "r+" );

    if ( NULL == fp ) {
        perror ( "/root/no_exit_file" );
    }

    return 0;
}
```

执行结果：

``` bash
[root@localhost io]# ./a.out
/root/noexitfile: No such file or directory
```