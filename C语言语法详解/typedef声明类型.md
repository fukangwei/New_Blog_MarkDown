---
title: typedef声明类型
date: 2018-12-10 16:50:56
categories: C语言语法详解
---
&emsp;&emsp;`typedef`的作用是声明一个新的类型名来代替已有的类型：

``` cpp
typedef int INTEGER; /* 用标识符INTEGER代表int类型 */
typedef float REAL; /* 用REAL代表float类型 */
```

这样以下两行等价：

``` cpp
int i, j; float a, b;
INTEGER i, j; REAL a, b;
```

&emsp;&emsp;在一个程序中，整型变量是专门用来计数的，可以用`COUNT`来作为整型类型名：

``` cpp
typedef int COUNT; /* 指定用COUNT代表int型 */
COUNT i, j; /* 将变量i、j定义为COUNT类型(即int类型)，可以使人更一目了然地知道它们是用于计数的 */
```

&emsp;&emsp;`typedef`也可以声明结构体类型：

``` cpp
/* 注意在struct之前用了关键字typedef，表示是声明新名 */
typedef struct {
    int month;
    int day;
    int year;
} DATE; /* 注意DATE是新类型名，而不是结构体变量名 */
```

所声明的新类型名`DATE`代表上面指定的一个结构体类型，这样就可以用`DATE`定义变量：

``` cpp
DATE birthday;
DATE* p; /* p为指向此结构体类型数据的指针 */
```

还可以进一步：

``` cpp
typedef int NUM[100];        // 声明NUM为整型数组类型，包含100个元素
NUM n;                       // 定义n为包含100个整型元素的数组
typedef char* STRING;        // 声明STRING为字符指针类型
STRING p, s[10];             // p为字符指针变量，s为指针数组(有10个元素)
typedef int ( *POINTER ) (); // 声明POINTER为指向函数的指针类型，函数返回整型值
POINTER p1, p2;              // p1、p2为POINTER类型的指针变量
```

&emsp;&emsp;`typedef`声明的方法如下：

1. 先按定义变量的方法写出定义语句，如`int i;`。
2. 再将变量名换成新类型名，如将`i`换成`COUNT`。
3. 在最前面加`typedef`，如`typedef int COUNT;`。
4. 然后可以用新类型名去定义变量。

&emsp;&emsp;再以声明上述的数组类型为例来说明：

1. 先按定义数组形式书写`int n[100];`；
2. 将变量名`n`换成自己指定的类型名`int NUM[100];`；
3. 在前面加上`typedef`，得到`typedef int NUM[100]`；
4. 然后用来定义变量`NUM n;`，`n`是包含`100`个整型元素的数组。

&emsp;&emsp;习惯上常把用`typedef`声明的类型名用大写字母表示，以便与系统提供的标准类型标识符相区别。`typedef`总结如下：

- `typedef`可以声明各种类型名，但不能用来定义变量。用`typedef`可以声明数组类型、字符串类型，使用比较方便。
- 用`typedef`只是对已经存在的类型增加一个类型名，而没有创造新的类型。
- 当在不同源文件中用到同一类型数据(尤其是像数组、指针、结构体、共用体等类型数据)时，常用`typedef`声明一些数据类型，把它们单独放在一个头文件中，然后在需要用到它们的文件中用`include`命令把它们包含进来，以提高编程效率。
- 使用`typedef`有利于程序的通用与移植。有时程序会依赖于硬件特性，使用`typedef`便于移植。