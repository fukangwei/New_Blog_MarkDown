---
title: ferror和perror函数
categories: C语言语法详解
date: 2018-12-13 11:30:24
---
### ferror函数

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
int ferror ( FILE* fp );
```

其功能是测试文件是否出现错误，返回值为`0`表示未出错，为`非0值`表示出错。
&emsp;&emsp;在调用各种输入输出函数时，如果出现错误，除了函数返回值有所体现外，还可以用`ferror`函数检查。

``` cpp
#include <stdio.h>
#include <stdlib.h>

int main ( void ) {
    FILE* pf = fopen ( "./test.txt", "r" );

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

&emsp;&emsp;函数原型如下：

``` cpp
void perror ( const char* s );
```

该函数用来将上一个函数发生错误的原因输出到`stderr`。参数`s`所指向的字符串会先打印，后面再加上错误原因字符串。

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
$./a.out
/root/noexitfile: No such file or directory
```