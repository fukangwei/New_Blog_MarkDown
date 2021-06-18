---
title: assert函数
categories: C语言语法详解
date: 2018-12-10 19:23:24
---
### 语法说明

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <assert.h>
void assert ( int expression );
```

其作用是先计算表达式`expression`，如果其值为`假`或为`0`，那么它将终止程序运行。

``` cpp
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>

int main ( void ) {
    FILE* fp;
    /* 以可写的方式打开一个文件，如果不存在，就创建一个同名文件 */
    fp = fopen ( "test.txt", "w" );
    assert ( fp ); /* 所以这里不会出错 */
    fclose ( fp );
    /* 以只读的方式打开一个文件，如果不存在，则打开文件失败 */
    fp = fopen ( "noexistfile.txt", "r" );
    assert ( fp ); /* 所以这里出错 */
    fclose ( fp ); /* 程序永远都执行不到这里 */
    return 0;
}
```

执行结果：

``` cpp
my_test: my_test.cpp:13: int main(): Assertion `fp' failed.
Aborted
```

### 禁用assert

&emsp;&emsp;频繁地调用`assert`会影响程序的性能，因此在调试结束后，可以通过如下方法来禁用`assert`：

``` cpp
#include <stdio.h>
#define NDEBUG
#include <assert.h>
```

### 参数检验

&emsp;&emsp;`assert`主要用于在函数开始处检验传入参数的合法性：

``` cpp
int resetBufferSize ( int nNewSize ) {
    assert ( nNewSize >= 0 );
    assert ( nNewSize <= MAX_BUFFER_SIZE );
    ...
}
```