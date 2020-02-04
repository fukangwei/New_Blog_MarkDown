---
title: C指针
categories: C语言语法详解
abbrlink: 23521
date: 2019-10-02 11:01:18
---
### 变量地址

&emsp;&emsp;每一个变量都有一个内存位置，这些位置可以使用`&`运算符获取：<!--more-->

``` cpp
#include <stdio.h>

int main() {
    int var;
    printf ( "var变量的地址：%p\n", &var );
    return 0;
}
```

执行结果：

``` cpp
var变量的地址：00CFFCCC
```

### 什么是指针？

&emsp;&emsp;指针是一个变量，其值为另一个变量的地址。就像其他变量或常量一样，必须在使用指针存储其他变量地址之前，对其进行定义：

``` cpp
type *var_name;
```

`type`是指针的基类型，它必须是一个有效的`C`数据类型，`var_name`是指针变量的名称。用来声明指针的星号`*`与乘法中使用的星号是相同的。但是，在这个语句中，星号是用来指定一个变量是指针。以下是有效的指针声明：

``` cpp
int    *ip; /* 一个整型的指针     */
double *dp; /* 一个double型的指针 */
float  *fp; /* 一个浮点型的指针   */
char   *ch; /* 一个字符型的指针   */
```

所有实际数据类型，不管是整型、浮点型、字符型，还是其他的数据类型，对应指针的值的类型都是一样的，都是一个代表内存地址的长的十六进制数。不同数据类型的指针之间唯一的不同是，指针所指向的变量或常量的数据类型不同。

### 如何使用指针？

&emsp;&emsp;使用指针时会频繁进行以下几个操作：

1. 定义一个指针变量。
2. 把变量地址赋值给指针。
3. 访问指针变量中可用地址的值。

可以使用一元运算符`*`来返回指针所指向变量的内容。

``` cpp
#include <stdio.h>

int main() {
    int var = 20; /* 实际变量的定义 */
    int* ip; /* 指针变量的定义 */
    ip = &var; /* 在指针变量中存储var的地址 */
    printf ( "Address of var variable: %p\n", &var );
    /* 在指针变量中存储的地址 */
    printf ( "Address stored in ip variable: %p\n", ip );
    /* 使用指针访问值 */
    printf ( "Value of *ip variable: %d\n", *ip );
    return 0;
}
```

执行结果：

``` cpp
Address of var variable: bffd8b3c
Address stored in ip variable: bffd8b3c
Value of *ip variable: 20
```

### NULL指针

&emsp;&emsp;在变量声明的时候，如果没有确切的地址可以为其赋值，将指针变量赋为`NULL`是一个良好的编程习惯，表示不指向任何东西。`NULL`指针被称为`空指针`。
&emsp;&emsp;`NULL`指针是一个定义在标准库中的值为`0`的常量：

``` cpp
#include <stdio.h>

int main() {
    int* ptr = NULL;
    printf ( "ptr的地址是%p\n", ptr );
    return 0;
}
```

执行结果：

``` cpp
ptr的地址是0x0
```

如果需要检查一个指针是否为`NULL`，可以使用`if`语句：

``` cpp
if ( ptr )  /* 如果p不为NULL，则为True */
if ( !ptr ) /* 如果p为NULL，则为True   */
```