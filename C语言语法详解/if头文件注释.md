---
title: if头文件注释
categories: C语言语法详解
date: 2018-12-14 23:37:57
---
### 注释方法

&emsp;&emsp;当注释掉大块代码时，使用`#if 0`比使用`/* */`要好，因为可能会出现被注释掉的代码中有嵌套的`/* */`，这会导致注释掉的代码区域不是你想要的范围：<!--more-->

``` cpp
#include "stdio.h"

int main() {
    int a = 11;
    /* 这是一个外层注释。
        /*
            int *b = &a; // 这是一个内层代码注释
            *b = 10;
        */
    a++;
    */
}
```

上面的程序编译后，发现缺少了一个注释符号，因为`/*`是根据最近的`*/`来判断注释的区域。
&emsp;&emsp;所以人们就使用了`#if 0`：

``` cpp
#include "stdio.h"

int main ( void ) {
    int a = 11;
#if 0
    int* b = &a;
    *b = 10;
#endif
    a++;
}
```

### 条件编译

&emsp;&emsp;对条件编译(`#ifdef`、`#else`、`#endif`、`#if`等)的说明如下：
&emsp;&emsp;情况`1`如下：

``` cpp
#ifdef _XXXX
    /* 程序段1 */
#else
    /* 程序段2 */
#endif
```

如果`_XXXX`被定义过，则对`程序段1`进行编译，否则对`程序段2`进行编译。
&emsp;&emsp;情况`2`如下：

``` cpp
#ifndef _XXXX
    /* 程序段1 */
#else
    /* 程序段2 */
#endif
```

如果`_XXXX`没有被定义过，则对`程序段1`进行编译，否则对`程序段2`进行编译。
&emsp;&emsp;情况`3`如下：

``` cpp
#if _XXXX
    /* 程序段1 */
#else
    /* 程序段2 */
#endif
```

如果`_XXXX`为`真`，则对`程序段1`进行编译，否则对`程序段2`进行编译。