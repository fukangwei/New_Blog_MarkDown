---
title: C存储类
date: 2019-10-02 05:49:48
categories: C语言语法详解
---
&emsp;&emsp;存储类定义`C`程序中变量/函数的范围和生命周期，这些说明符放置在它们所修饰的类型之前。下面列出`C`程序中可用的存储类：

- auto
- register
- static
- extern

### auto

&emsp;&emsp;`auto`存储类是所有局部变量默认的存储类：

``` cpp
{
    int mount;
    auto int month;
}
```

上面的实例定义了两个带有相同存储类的变量，`auto`只能用在函数内，即`auto`只能修饰局部变量。

### register

&emsp;&emsp;`register`存储类用于定义存储在寄存器中而不是`RAM`中的局部变量。这意味着变量的最大尺寸等于寄存器的大小，且不能对它应用一元的`&`运算符，因为它没有内存位置。

``` cpp
register int miles;
```

寄存器只用于需要快速访问的变量，比如计数器。还应注意的是，定义`register`并不意味着变量将被存储在寄存器中，它意味着变量可能存储在寄存器中，这取决于硬件和实现的限制。

### static

&emsp;&emsp;`static`存储类指示编译器在程序的生命周期内保持局部变量的存在，而不需要在每次它进入和离开作用域时进行创建和销毁。因此，使用`static`修饰局部变量可以在函数调用之间保持局部变量的值。
&emsp;&emsp;`static`修饰符也可以应用于全局变量。当`static`修饰全局变量时，会使变量的作用域限制在声明它的文件内。

### extern

&emsp;&emsp;`extern`存储类用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。
&emsp;&emsp;当你有多个源文件，并且定义了一个可以在其他文件中使用的全局变量或函数时，可以在其他文件中使用`extern`来得到已定义的变量的引用。可以这么理解，`extern`是用来在另一个源文件中声明一个全局变量。
&emsp;&emsp;`extern`修饰符通常用于有多个源文件共享相同的全局变量的时候：

``` cpp
/* support.c */
int count = 5;

/* main.c */
#include <stdio.h>

extern int count;

int main() {
    printf ( "%d\n", count ); /* 输出“5” */
}
```