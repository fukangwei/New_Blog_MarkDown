---
title: typedef声明类型
categories: C语言语法详解
date: 2018-12-10 16:50:56
---
### 基本作用

&emsp;&emsp;`typedef`的作用是为已有的类型起一个别名：<!--more-->

``` cpp
typedef int INTEGER; // 用INTEGER代表int类型
typedef float REAL;  // 用REAL代表float类型
```

因此，以下两行代码是等价的：

``` cpp
int i, j;
INTEGER i, j;
```

### 声明结构体

&emsp;&emsp;`typedef`也可以声明结构体类型：

``` cpp
typedef struct {
    int month;
    int day;
    int year;
} DATE; /* DATE是新类型名，而不是结构体变量 */
```

这样就可以用`DATE`定义结构体变量：

``` cpp
DATE birthday;
DATE* p;
```

### 声明数组

&emsp;&emsp;声明数组如下：

``` cpp
typedef int NUM[100]; // NUM为整型数组类型，包含100个元素
NUM n;                // 定义n为包含100个整型元素的数组
```

### 声明指针

&emsp;&emsp;声明指针如下：

``` cpp
typedef char* STRING;        // 声明STRING为字符指针类型
STRING p, s[10];             // p为字符指针变量；s为指针数组，有10个指针变量
typedef int ( *POINTER ) (); // 声明POINTER为指向函数的指针类型，函数返回整型值
POINTER p1, p2;              // p1、p2为POINTER类型的指针变量
```