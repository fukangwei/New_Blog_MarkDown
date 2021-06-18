---
title: C数组
categories: C语言语法详解
date: 2019-10-02 10:11:55
---
&emsp;&emsp;`C`语言支持数组数据结构，它可以存储一个固定大小的相同类型元素的顺序集合。<!--more-->
&emsp;&emsp;所有的数组都是由连续的内存位置组成。最低的地址对应第一个元素，最高的地址对应最后一个元素。

### 声明数组

&emsp;&emsp;在`C`语言中要声明一个数组，需要指定元素的类型和元素的数量：

``` cpp
type arrayName[arraySize];
```

这叫做`一维数组`。`arraySize`必须是一个大于`0`的整数常量，`type`可以是任意有效的数据类型。
&emsp;&emsp;例如，声明一个类型为`double`的包含`10`个元素的数组`balance`：

``` cpp
double balance[10];
```

### 初始化数组

&emsp;&emsp;在`C`语言中，你可以逐个初始化数组，也可以使用一个初始化语句：

``` cpp
double balance[5] = {1000.0, 2.0, 3.4, 7.0, 50.0};
```

大括号`{}`之间的值的数目不能大于在数组声明时在方括号`[]`中指定的元素数目。
&emsp;&emsp;如果你省略掉了数组的大小，数组的大小则为初始化时元素的个数：

``` cpp
double balance[] = {1000.0, 2.0, 3.4, 7.0, 50.0};
```

&emsp;&emsp;下面是一个为数组中某个元素赋值的实例，把数组中第`5`个元素的值赋为`50.0`：

``` cpp
balance[4] = 50.0;
```

&emsp;&emsp;所有的数组都是以`0`作为它们第一个元素的索引，也被称为`基索引`；数组的最后一个索引是数组的总大小减去`1`。

### 访问数组元素

&emsp;&emsp;数组元素可以通过`数组名称 + 索引`进行访问：

``` cpp
double salary = balance[9];
```

上面的语句将把数组中第`10`个元素的值赋给变量`salary`。

``` cpp
#include <stdio.h>

int main() {
    int n[5];
    int i, j;

    for ( i = 0; i < 5; i++ ) {
        n[i] = i + 100;
    }

    for ( j = 0; j < 5; j++ ) {
        printf ( "Element[%d] = %d\n", j, n[j] );
    }

    return 0;
}
```

执行结果：

``` cpp
Element[0] = 100
Element[1] = 101
Element[2] = 102
Element[3] = 103
Element[4] = 104
```