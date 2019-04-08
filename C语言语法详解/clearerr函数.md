---
title: clearerr函数
date: 2018-12-12 00:32:53
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C库函数之clearerr](http://wiki.jikexueyuan.com/project/c/clearerr.html)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;clearerr函数清除给定流stream的文件结束和错误标识符。下面是clearerr函数的声明：

``` c
void clearerr ( FILE* stream );
```

参数stream是指向FILE对象的指针，该FILE对象标识了流。如果它检测到它的参数不是一个有效的流，则返回`-1`，并设置errno为EBADF。

``` c
#include <stdio.h>

int main() {
    FILE* fp;
    char c;
    fp = fopen ( "file.txt", "w" );
    c = fgetc ( fp );

    if ( ferror ( fp ) ) {
        printf ( "读取文件file.txt时发生错误(first)\n" );
    }

    clearerr ( fp );

    if ( ferror ( fp ) ) {
        printf ( "读取文件file.txt时发生错误(second)\n" );
    }

    fclose ( fp );
    return ( 0 );
}
```

假设我们有一个文本文件`file.txt`，它是一个空文件。当试图读取一个以只写模式打开的文件，这将产生以下结果：

``` bash
读取文件file.txt时发生错误(first)
```

若去掉`clearerr(fp);`，则执行结果为：

``` bash
读取文件file.txt时发生错误(first)
读取文件file.txt时发生错误(second)
```