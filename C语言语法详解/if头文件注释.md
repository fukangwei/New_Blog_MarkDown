---
title: if头文件注释
categories: C语言语法详解
date: 2018-12-14 23:37:57
---
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

上面的程序编译后发现缺少了一个注释符号，因为注释符头`/*`是根据最近结束符`*/`来判断注释的区域的，但是一旦内嵌了就会发现错误。所以人们就使用了`#if 0`：

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

&emsp;&emsp;在这里顺便对条件编译(`#ifdef`、`#else`、`#endif`、`#if`等)进行说明。主要分为`3`种情况：
&emsp;&emsp;情况`1`如下：

``` cpp
#ifdef _XXXX
    /* 程序段1 */
#else
    /* 程序段2 */
#endif
```

如果标识符`_XXXX`被定义过，则对`程序段1`进行编译；否则对`程序段2`进行编译。
&emsp;&emsp;情况`2`如下：

``` cpp
#ifndef _XXXX
    /* 程序段1 */
#else
    /* 程序段2 */
#endif
```

这里使用了`#ifndef`，它和`#ifdef`相反。
&emsp;&emsp;情况`3`如下：

``` cpp
#if 常量
    /* 程序段1 */
#else
    /* 程序段2 */
#endif
```

如果常量为`真`，就执行`程序段1`，否则执行`程序段2`。