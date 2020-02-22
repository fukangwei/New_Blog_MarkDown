---
title: C函数
categories: C语言语法详解
date: 2019-10-02 09:02:15
---
&emsp;&emsp;函数是一组一起执行某个任务的语句。每个`C`程序都至少有一个函数，即主函数`main`。`C`语言允许用户自己定义函数。<!--more-->
&emsp;&emsp;你可以把代码划分到不同的函数中。如何划分代码到不同的函数中是由你来决定的，但在逻辑上，划分通常是根据每个函数执行一个特定的任务来进行的。
&emsp;&emsp;函数声明告诉编译器函数名称、返回类型和参数。函数定义提供了函数的实际主体。

### 定义函数

&emsp;&emsp;`C`语言函数定义的一般形式如下：

``` cpp
return_type function_name( parameter list ) {
    body of the function
}
```

- `返回类型`：一个函数可以返回一个值，`return_type`是函数返回的值的数据类型。有些函数执行所需的操作而不返回值，在这种情况下，`return_type`是关键字`void`。
- `函数名称`：这是函数的实际名称。
- `参数`：参数就像是占位符，当函数被调用时，你向参数传递一个值，这个值被称为`实际参数`。参数列表包括函数参数的类型、顺序以及数量。参数是可选的，也就是说，函数可能不包含参数。
- `函数主体`：函数主体包含一组定义函数执行任务的语句。

以下是`max`函数的源代码，该函数有两个参数`num1`和`num2`，会返回这两个数中较大的那个数：

``` cpp
/* 函数返回两个数中较大的那个数 */
int max ( int num1, int num2 ) {
    int result; /* 局部变量声明 */

    if (num1 > num2)
        result = num1;
    else
        result = num2;

    return result;
}
```

### 函数声明

&emsp;&emsp;函数声明会告诉编译器函数名称及如何调用函数。函数的实际主体可以单独定义。
&emsp;&emsp;函数声明包括以下几个部分：

``` cpp
return_type function_name ( parameter list );
```

针对上面定义的函数`max`，以下是函数声明：

``` cpp
int max ( int num1, int num2 );
```

在函数声明中，参数的名称并不重要，只有参数的类型是必需的，因此下面也是有效的声明：

``` cpp
int max ( int, int );
```

当你在一个源文件中定义函数且在另一个文件中调用函数时，函数声明是必需的。在这种情况下，你应该在调用函数的文件顶部声明函数。

### 调用函数

&emsp;&emsp;当程序调用函数时，程序控制权会转移给被调用的函数。被调用的函数执行已定义的任务，当函数的`return`语句被执行，或者到达函数的结束括号时，会把程序控制权交还给主程序。
&emsp;&emsp;调用函数时，传递所需参数，如果函数返回一个值，则可以存储返回值：

``` cpp
#include <stdio.h>

int max ( int num1, int num2 ); /* 函数声明 */

int main() {
    int a = 100;
    int b = 200;
    int ret;

    ret = max ( a, b ); /* 调用函数来获取最大值 */
    printf ( "Max value is: %d\n", ret );
    return 0;
}

int max ( int num1, int num2 ) {
    int result;

    if ( num1 > num2 )
        result = num1;
    else
        result = num2;

    return result;
}
```

执行语句：

``` cpp
Max value is: 200
```