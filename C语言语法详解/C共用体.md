---
title: C共用体
categories: C语言语法详解
date: 2019-10-02 13:18:13
---
&emsp;&emsp;共用体是一种特殊的数据类型，允许在相同的内存位置存储不同的数据类型。可以定义一个带有多成员的共用体，但是任何时候只能有一个成员带有值。<!--more-->

### 定义共用体

&emsp;&emsp;`union`语句的格式如下：

``` cpp
union tag {
    member-list
    member-list
    ...
} [one or more union variables];
```

&emsp;&emsp;下面定义一个名为`Data`的共用体类型：

``` cpp
union Data {
    int i;
    float f;
    char str[20];
} data;
```

现在`Data`类型的变量可以存储一个整数、一个浮点数或者一个字符串，这意味着相同的内存位置可以存储多个多种类型的数据。
&emsp;&emsp;共用体占用的内存应足够存储共用体中最大的成员。在上面的实例中，`Data`将占用`20`个字节的内存空间。

``` cpp
#include <stdio.h>
#include <string.h>

union Data {
    int i;
    float f;
    char str[20];
};

int main() {
    union Data data;
    printf ( "Memory size occupied by data: %d\n", sizeof ( data ) );
    return 0;
}
```

执行结果：

``` cpp
Memory size occupied by data: 20
```

### 访问共用体成员

&emsp;&emsp;可以使用成员访问运算符`.`来访问共用体成员：

``` cpp
#include <stdio.h>
#include <string.h>

union Data {
    int i;
    float f;
    char str[20];
};

int main() {
    union Data data;
    data.i = 10;
    printf ( "data.i : %d\n", data.i );
    data.f = 220.5;
    printf ( "data.f : %f\n", data.f );
    strcpy ( data.str, "C Programming" );
    printf ( "data.str : %s\n", data.str );
    return 0;
}
```

执行结果：

``` cpp
data.i : 10
data.f : 220.500000
data.str : C Programming
```