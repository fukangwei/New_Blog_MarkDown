---
title: 头文件cplusplus的解释
date: 2018-12-09 12:54:38
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[关于#ifdef __cplusplus和extern "C"的问题](https://blog.csdn.net/u012234115/article/details/43272441)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;我们经常会遇到下面的代码段：

``` c
#ifdef __cplusplus
extern "C" {
#endif
... /* c语法代码段 */
#ifdef __cplusplus
}
#endif
```

`__cplusplus`是CPP中的自定义宏，表示这是一段cpp的代码，编译器按C++的方式进行编译。如果这时需要使用C语言的代码，那么就需要加上`extern "C" {`来说明。

``` c
#ifdef __cplusplus /* C++编译环境中才会定义“__cplusplus” */
    /* 告诉编译器下面的函数是C语言函数(因为C++和C语言
       对函数的编译转换不一样，主要是因为C++中存在重载) */
    extern"C" {
#endif
```

&emsp;&emsp;要明白为何使用`extern "C"{`，还得从CPP中对函数的重载处理开始说起。在C++中，为了支持重载机制，在编译生成的汇编码中，要对函数的名字进行一些处理，加入比如函数的返回类型等。而在C中，只是简单的函数名字而已，不会加入其他的信息。也就是说，C++和C对产生的函数名字的处理是不一样的。加上`extern "C"{`的目的，就是主要实现C与C++的相互调用问题。`extern "C"`是连接声明(linkage declaration)，被其修饰的变量和函数是按照C语言方式编译和连接的。作为一种面向对象的语言，C++支持函数重载，而C语言不支持。假设某个函数的原型为：

``` c
void foo ( int x, int y );
```

该函数被C编译器编译后，在symbol库中的名字为`_foo`，而C++编译器则会产生像`_foo_int_int`之类的名字。`_foo_int_int`这样的名字包含了函数名和函数参数数量及类型信息，C++就是靠这种机制来实现函数重载的。

### C++调用C函数的实例

&emsp;&emsp;c.h如下所示：

``` c
#ifndef _c_h_
#define _c_h_

#ifdef __cplusplus
extern "C" {
#endif

void C_fun();

#ifdef __cplusplus
}
#endif

#endif
```

&emsp;&emsp;cc.c如下所示：

``` c
#include "c.h"

void C_fun() {
}
```

需要在`cxx.cpp`中调用`c.c`中的C_fun。`cxx.cpp`如下所示：

``` c
#include "c.h"

int main() {
    C_fun();
}
```

C++调用C的函数时，在C++的`.h`文件中加`extern "C" {}`。`extern "C"`是告诉C++编译器件括号里的东西是按照C的obj文件格式编译的，要连接的话按照C的命名规则去找。

### C调用C++中的函数

&emsp;&emsp;因为先有C后有C++，所以只能从C++的代码中考虑了。加入C++中的函数或变量有可能被C中的文件调用，也是用`extern "C"{}`，不过代码中要加，头文件中也要加，因为可能C++中也调用该函数。`Cxx.h`如下所示：

``` c
#ifndef _c_h_
#define _c_h_

#ifdef __cplusplus
extern "C" {
#endif

void CPP_fun();

#ifdef __cplusplus
}
#endif

#endif
```

`Cxx.cpp`如下所示：

``` c
extern "C" { /* 告诉C+++编译器，括号里按照C的命名规则编译 */
    void CPP_fun() {
        /* user code */
    }
}
```

c.c如下所示：

``` c
#include "Cpp.h"

int main() {
    CPP_fun();
}
```