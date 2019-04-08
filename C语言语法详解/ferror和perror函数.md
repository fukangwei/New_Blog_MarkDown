---
title: ferror和perror函数
date: 2018-12-13 11:30:24
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[ferror](https://baike.baidu.com/item/ferror)，内容经过测试和修改，感谢原作者。

### ferror函数

&emsp;&emsp;ferror的函数原型为：

``` c
int ferror ( FILE* fp );
```

其功能是测试文件是否出现错误。返回值为未出错是0，出错为非0值。
&emsp;&emsp;在调用各种输入输出函数(例如putc、getc、fread和fwrite等)时，如果出现错误，除了函数返回值有所反映外，还可以用ferror函数检查。对同一个文件(文件指针或文件描述符)，每一次调用输入输出函数，均产生一个新的ferror函数值，因此应当在调用输入输出函数后立即检查ferror函数的值，否则信息会丢失。fopen打开文件时，ferror函数处置自动置为0。

``` c
#include <stdio.h>
#include <stdlib.h>
​
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
    ​
    fputs ( "1234", pf );

    if ( ferror ( pf ) == 0 ) {
        printf ( "\n正常" );
    } else {
        printf ( "\n异常" );
    }

    system ( "pause" );
}
```

### perror函数

&emsp;&emsp;perror的函数原型为：

``` c
void perror ( const char* s );
```

perror用来将上一个函数发生错误的原因输出到标准设备(stderr)。参数s所指的字符串会先打印出，后面再加上错误原因字符串。此错误原因依照全局变量error的值来决定要输出的字符串。在库函数中有个error变量，每个error值对应着以字符串表示的错误类型。当你调用某些函数出错时，该函数已经重新设置了error的值。perror函数只是将你输入的一些信息和现在的error所对应的错误一起输出。

``` c
#include <stdio.h>
​
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