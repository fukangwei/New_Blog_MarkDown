---
title: Linux下C语言结构体初始化
categories: C语言语法详解
abbrlink: e038e3f5
date: 2018-12-10 09:57:15
---
&emsp;&emsp;今天看到一段代码，初始化一个`struct`用的是乱序格式：<!--more-->

``` cpp
typedef struct _data_t {
    int a;
    int b;
} data_t;

data_t data = {
    .a = 10,
    .b = 20,
};
```

通常情况下，初始化一个结构体的方式是按序初始化，例如`data_t data = {10, 20};`。在网上查找资料，发现`Linux`的`struct`初始化可以采用`顺序`和`乱序`两种方式，而乱序又有两种不同的形式。本文总结一下`struct`两种初始化方式的优缺点，并给出完整的测试程序。

### 顺序初始化

&emsp;&emsp;顺序初始化`struct`必须要按照成员的顺序进行，缺一不可。如果结构体比较大，很容易出现错误，而且表现形式不直观，不能一眼看出各个`struct`各个数据成员的值。

### 乱序初始化

&emsp;&emsp;乱序初始化是`C99`标准新加的，比较直观的一种初始化方式。相对于顺序初始化而言，乱序初始化的结构体成员可以不按照顺序初始化，而且可以只初始化部分成员，扩展性较好。`Linux`内核中采用这种方式初始化`struct`。
&emsp;&emsp;乱序初始化有两种方式，一种是用点`.`符号，一种是用冒号`:`。方式`1`是`C99`标准，方式`2`是`GCC`的扩展，强烈建议使用第一种方式。示例代码如下(这段代码在`Linux`下可以用`gcc`直接进行编译)：

``` cpp
#include <stdio.h>

typedef int ( *caculate_cb ) ( int a, int b ); /* 函数指针 */

typedef struct _oper { /* 结构体定义 */
    int a;
    int b;
    caculate_cb cal_func;
} oper;

int add ( int a, int b ) { /* 加法函数定义 */
    return ( a + b );
}

int main ( void ) {
    int ret = 0;
    oper oper_one = {10, 20, add}; /* 顺序初始化结构体 */

    oper oper_two = { /* 乱序初始化结构体 */
        .b = 30,
        .a = 20,
        .cal_func = &add,
    };

    oper oper_three = { /* 乱序初始化结构体 */
        cal_func: &add,
        a: 40,
        b: 20,
    };

    ret = oper_one.cal_func ( oper_one.a, oper_one.b );
    printf ( "oper_one caculate: ret = %d\n", ret );
    ret = oper_two.cal_func ( oper_two.a, oper_two.b );
    printf ( "oper_two caculate: ret = %d\n", ret );
    ret = oper_three.cal_func ( oper_three.a, oper_three.b );
    printf ( "oper_three caculate: ret = %d\n", ret );
    return 0;
}
```

执行结果：

``` bash
oper_one caculate: ret = 30
oper_two caculate: ret = 50
oper_three caculate: ret = 60
```

---

&emsp;&emsp;在阅读`GNU/Linux`内核代码时，我们会遇到一种特殊的结构初始化方式。该方式是某些`C`教材(如`K&R`第二版)中没有介绍过的，称为指定初始化(`designated initializer`)。下面看一个例子，`Linux-2.6.x/drivers/usb/storage/usb.c`中有这样一个结构体初始化代码：

``` cpp
static struct usb_driver usb_storage_driver = {
    .owner = THIS_MODULE,
    .name = "usb-storage",
    .probe = storage_probe,
    .disconnect = storage_disconnect,
    .id_table = storage_usb_ids,
};
```

这就是前面所说的指定初始化在`Linux`设备驱动程序中的一个应用，它源自`C99`标准。以下我摘录了`C Primer Plus`第五版中相关章节的内容，从而就可以很好的理解`2.6`版内核采用这种方式的优势就在于由此初始化不必严格按照定义时的顺序。
&emsp;&emsp;已知一个结构，定义如下：

``` cpp
struct book {
    char title[MAXTITL];
    char author[MAXAUTL];
    float value;
};
```

`C99`支持结构的指定初始化项目，其语法与数组的指定初始化项目近似，只是结构的指定初始化项目使用`点运算符`和`成员名`(而不是方括号和索引值)来标识具体的元素。例如只初始化`book`结构的成员`value`，可以这样做：

``` cpp
struct book surprise = {
    .value = 10.99
};
```

可以按照任意的顺序使用指定初始化项目：

``` cpp
struct book gift = {
    .value = 25.99,
    .author = "James Broadfool",
    .title = "Rue for the Toad"
};
```

正像数组一样，跟在一个指定初始化项目之后的常规初始化项目为跟在指定成员后的成员提供了初始值。另外，对特定成员的最后一次赋值是它实际获得的值。例如考虑下列声明：

``` cpp
struct book gift = {
    .value = 18.90,
    .author = "Philionna pestle",
    0.25
};
```

这将把值`0.25`赋给成员`value`，因为它在结构声明中紧跟在`author`成员之后，新的值`0.25`代替了原先的赋值`18.90`。