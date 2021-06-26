---
title: stat、fstat和lstat函数
categories: Linux系统编程
date: 2019-02-03 16:33:46
---
&emsp;&emsp;函数`stat`、`fstat`和`lstat`用于检查文件的属性。<!--more-->

### stat

&emsp;&emsp;函数原型如下：

``` cpp
#include <sys/stat.h>
int stat ( const char *filename, struct stat *buf );
```

该函数将`filename`的文件属性信息存储在`buf`指向的结构体中。如果函数执行成功，则返回值为`0`，失败则返回`-1`。
&emsp;&emsp;如果`filename`是一个符号链接，那么`buf`指向的结构体将存储该链接指向的文件的属性信息。

### fstat

&emsp;&emsp;函数原型如下：

``` cpp
#include <sys/stat.h>
int fstat ( int filedes, struct stat *buf );
```

该函数与`stat`类似，区别是使用文件描述符`filedes`来代替文件名。如果函数执行成功，则返回值为`0`，失败则返回`-1`。

### lstat

&emsp;&emsp;函数原型如下：

``` cpp
#include <sys/stat.h>
int lstat ( const char *filename, struct stat *buf );
```

该函数与`stat`函数类似，如果`filename`是一个符号链接，则返回该链接符号的属性信息。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <sys/stat.h>
#include <unistd.h>
#include "stdio.h"

int main() {
    struct stat buf;
    stat ( "/etc/passwd", &buf );
    printf ( "/etc/passwd file size = %ld\n", buf.st_size );
}
```