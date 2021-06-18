---
title: C存储类
categories: C语言语法详解
date: 2019-10-02 05:49:48
---
&emsp;&emsp;存储类定义`C`程序中变量或函数的范围和生命周期，这些说明符放置在它们所修饰的类型之前。<!--more-->

### auto

&emsp;&emsp;`auto`是所有局部变量默认的存储类。下面定义两个带有相同存储类的变量：

``` cpp
{
    int mount;
    auto int month;
}
```

`auto`只能用在函数内，即`auto`只能修饰局部变量。

### register

&emsp;&emsp;`register`用于定义存储在寄存器中，而不是`RAM`中的局部变量。这意味着变量的最大尺寸等于寄存器的大小，且不能对它应用一元的`&`运算符，因为它没有内存位置。

``` cpp
register int miles;
```

&emsp;&emsp;定义`register`并不意味着变量将被存储在寄存器中，它仅意味着变量可能存储在寄存器中，这取决于硬件和实现的限制。

### static

&emsp;&emsp;`static`指示编译器在程序的生命周期内保持局部变量的存在。
&emsp;&emsp;`static`修饰符也可以应用于全局变量。当`static`修饰全局变量时，会使变量的作用域限制在声明它的文件内。

### extern

&emsp;&emsp;`extern`用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。
&emsp;&emsp;`support.c`如下：

``` cpp
int count = 5;
```

&emsp;&emsp;`main.c`如下：

``` cpp
#include <stdio.h>

extern int count;

int main() {
    printf ( "%d\n", count ); /* 输出“5” */
}
```