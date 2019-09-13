---
title: Linux下创建临时文件
date: 2018-12-29 16:24:08
categories: Linux系统编程
---
&emsp;&emsp;临时文件是一个暂时用来存储数据的文件。如果使用建立普通文件的方法来创建文件，则可能遇到文件是否存在、是否有文件读写权限的问题。`Linux`系统下提供的建立唯一的临时文件的方法如下：

``` c
#include <stdio.h>
​
char *tmpnam ( char *s );
FILE *tmpfile();
```

&emsp;&emsp;函数`tmpnam`产生一个唯一的文件名，如果参数为`NULL`，则在一个内部使用的缓冲区内产生文件名，当下一次调用该函数的时候，则可能覆盖该文件名；如果参数不是为`NULL`，则名字拷贝到字符串中，并把它作为函数值返回，字符串的长度至少为`L_tmpnam`，它定义在`stdio.h`中。如果创建不成功，则函数返回`NULL`。注意在执行的过程中，最多只能确保生成`TMP_MAX`个不同的名字，`tmpnam`只是用于创建一个名字，而不是创建一个文件。
&emsp;&emsp;函数`tmpfile`则返回一个临时文件的描述符，文件的打开属性是读和写，相当于使用`fopen`以`wb+`方式打开。如果创建不成功，则返回`NULL`。创建的临时文件将在被关闭或程序正常结束时将被自动删除。

``` c
#include "stdio.h"
#include "stdlib.h"
​
int main ( void ) {
    char tmpname[L_tmpnam];
    char *filename;
    FILE *tmpfp;
    filename = tmpnam ( tmpname );
    printf ( "Temp file name = %s\n", filename );
    tmpfp = tmpfile();
​
    if ( tmpfp ) {
        printf ( "Open a temporary file OK!\n" );
    } else {
        perror ( "tmpfile\n" );
    }
​
    return 0;
}
```

&emsp;&emsp;此外还有两个函数也可以创建临时文件：

``` c
#include <stdio.h>
char *mktemp ( char *template );
int mkstemp ( char *template );
```

函数`mktemp`从给定的模块`template`中创建唯一的临时文件名。模版可以是文件路径的前缀，模版的最后`6`个字符必须是`XXXXXX`：

``` c
char template[L_tmpnam];
char *filename;

strcpy ( template, "/temp/wypXXXXXX" );
filename = mktemp ( template );
printf ( "Temporary file name is %s\n", filename );
```

函数`mkstemp`类似于`tmpfile`，但是打开的文件相当于使用提供底层的文件操作函数`open`打开文件。