---
title: new和delete
categories: C++语法详解
date: 2019-02-07 10:44:54
---
&emsp;&emsp;在`C++`中，可以使用`new`在程序运行时，动态地分配堆中的内存，它会返回所分配的空间地址。<!--more-->
&emsp;&emsp;如果不再需要动态分配的内存空间，可以使用`delete`删除之前由`new`分配的内存。
&emsp;&emsp;下面是使用`new`来为任意的数据类型动态分配内存的方法：

``` cpp
new data-type;
```

`data-type`可以是包括数组在内的任意内置的数据类型，也可以是包括类或结构在内的用户自定义的任何数据类型。

### 分配普通数据类型

&emsp;&emsp;为普通数据类型动态分配内存的方法如下：

``` cpp
double* pvalue = NULL;
pvalue = new double;
...
delete pvalue;
```

### 分配数组数据类型

&emsp;&emsp;为数组动态分配内存的方法如下：

``` cpp
char* pvalue = NULL;
pvalue = new char[20]; /* 有20个char型元素的数组 */
...
delete [] pvalue;
```

### 补充说明

1. `delete`只会调用一次析构函数，而`delete[]`会调用每一个成员的析构函数。
2. 对于`int *pi1 = new int;`，进行的是默认初始化，`*pi1`的值未定义；对于`int *pi2 = new int ( 1024 );`，`*p2`的值初始化为`1024`。
3. 对于`int *p2 = new int[10]();`，不仅申请了空间，而且初始化为`0`；对于`int *p1 = new int[10];`，只申请空间，没有进行初始化。
4. `malloc`在`C++`中仍然存在，但建议尽量不要使用`malloc`。`new`与`malloc`函数相比，其主要的优点是：`new`不只是分配了内存，它还创建了对象。