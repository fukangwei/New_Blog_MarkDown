---
title: 头文件cplusplus的解释
categories: C语言语法详解
date: 2018-12-09 12:54:38
---
### 原因解释

&emsp;&emsp;我们经常会遇到下面的代码段：<!--more-->

``` cpp
#ifdef __cplusplus
extern "C" {
#endif
... /* C语法代码段 */
#ifdef __cplusplus
}
#endif
```

`__cplusplus`是`C++`中的自定义宏，表示这是一段`cpp`的代码，编译器按`C++`的方式进行编译。如果要弄明白`extern "C"{`的作用，还要从`C++`中对函数重载处理说起。
&emsp;&emsp;为了支持重载机制，`C++`在编译生成的符号表中，要对函数的名字进行一些处理，例如加上函数的参数类型。而在`C`语言中，只是简单的函数名字，不会加入其他的信息。也就是说，`C++`和`C`语言对产生的函数名字的处理是不一样的。
&emsp;&emsp;假设某个函数的原型为：

``` cpp
void foo ( int x, int y );
```

该函数被`C`编译器编译后，在符号表中的名字为`_foo`，而`C++`编译器则会产生像`_foo_int_int`之类的名字。`_foo_int_int`包含了函数名、参数数量以及参数类型信息，`C++`就是靠这种机制来实现函数重载的。
&emsp;&emsp;`extern "C"`的主要作用就是实现`C++`代码调用其他`C`语言代码。加上`extern "C"`后，会指示编译器这部分代码按`C`语言的方式进行编译。
&emsp;&emsp;例如你用`C`语言开发了一个`DLL`，为了让`C++`也能够调用该`DLL`中的函数，你需要用`extern "C"`来强制编译器不要修改你的函数名。

### 代码实例

&emsp;&emsp;`cExample.h`如下：

``` cpp
#ifndef C_EXAMPLE_H
#define C_EXAMPLE_H

#ifdef __cplusplus
extern "C" {
#endif
extern int add ( int x, int y );
#ifdef __cplusplus
}
#endif

#endif
```

&emsp;&emsp;`cExample.c`如下：

``` cpp
#include "cExample.h"

int add ( int x, int y ) {
    return x + y;
}
```

&emsp;&emsp;`cppFile.cpp`如下：

``` cpp
#include "cExample.h"
#include <iostream>

using namespace std;

int main ( int argc, char* argv[] ) {
    cout << "2 + 3 = " << add ( 2, 3 ) << endl;
    return 0;
}
```

&emsp;&emsp;使用`gcc`编译生成动态链接库：

``` bash
gcc -shared cExample.c -o libhello.so
```

&emsp;&emsp;使用`g++`编译`cppFile.cpp`，并链接`libhello.so`：

``` bash
export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH
g++ cppFile.cpp -o cppFile -lhello -L .
```

最后执行`./cppFile`即可。