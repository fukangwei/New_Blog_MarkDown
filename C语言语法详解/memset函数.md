---
title: memset函数
date: 2021-06-20 14:30:47
categories: C语言语法详解
---
### 函数原型

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <string.h>
void *memset ( void *s, int c, unsigned long n );
```

函数的功能是将指针变量`s`所指向的前`n`个字节的内存单元用`c`替换。

``` cpp
#include <string.h>
#include <stdio.h>

int main ( void ) {
    char buffer[] = "Hello world";
    printf ( "Buffer before memset: %s\n", buffer );
    memset ( buffer, '*', strlen ( buffer ) );
    printf ( "Buffer after memset: %s\n", buffer );
    return 0;
}
```

执行结果：

``` cpp
Buffer before memset: Hello world
Buffer after memset: ***********
```

### 常用方法

&emsp;&emsp;`memset`可以很方便地清空一个结构类型的变量或数组。
&emsp;&emsp;例如有这样的结构体：

``` cpp
struct sample_struct {
    char csName[16];
    int iSeq;
    int iType;
};

struct sample_struct stTest;
struct sample_struct TEST[10];
```

使用`memset`清空结构体变量或数组：

``` cpp
memset ( &stTest, 0, sizeof ( struct sample_struct ) );
memset ( TEST, 0, sizeof ( struct sample_struct ) * 10 );
```