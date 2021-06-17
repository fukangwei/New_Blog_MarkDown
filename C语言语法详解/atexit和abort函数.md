---
title: atexit和abort函数
categories: C语言语法详解
date: 2018-12-10 21:39:36
---
### atexit函数

&emsp;&emsp;在`main`函数退出之后，是否还可以执行程序？这时候就要提到函数`atexit`：<!--more-->

``` cpp
int atexit ( void ( *func ) ( void ) );
```

&emsp;&emsp;`atexit`函数称为`终止处理程序注册程序`，参数`func`是注册的函数。当`main`函数终止时，前面注册的各个函数将会被调用，且调用这些函数的顺序和它们登记的顺序是相反的。

``` cpp
#include <stdio.h>
#include <stdlib.h>

void func1 ( void ) {
    printf ( "in func1\n" );
}

void func2 ( void ) {
    printf ( "in func2\n" );
}

void func3 ( void ) {
    printf ( "in func3\n" );
}

int main() {
    atexit ( func3 );
    atexit ( func2 );
    atexit ( func1 );
    printf ( "In main\n" );
    return 0;
}
```

执行结果：

``` bash
In main
in func1
in func2
in func3
```

### abort函数

&emsp;&emsp;`abort`函数用于非正常地终止一个正在执行的程序：

``` cpp
void abort ( void );
```