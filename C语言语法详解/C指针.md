---
title: C指针
categories: C语言语法详解
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
var变量的地址：0x7fff21191924
```

### 什么是指针？

&emsp;&emsp;指针是一个变量，其值为另一个变量的地址。定义指针变量的方法如下：

``` cpp
type *var_name;
```

- `type`是指针的基类型，它必须是一个有效的`C`数据类型。
- `var_name`是指针变量的名称。

&emsp;&emsp;以下是有效的指针声明：

``` cpp
int    *ip; // 整型的指针
double *dp; // double型的指针
float  *fp; // 浮点型的指针
char   *ch; // 字符型的指针
```

### 如何使用指针？

&emsp;&emsp;使用指针时，会频繁进行以下几个操作：

1. 定义一个指针变量。
2. 把变量地址赋值给指针。
3. 访问指针变量中可用地址的值。

&emsp;&emsp;使用一元运算符`*`来返回指针所指向变量的内容：

``` cpp
#include <stdio.h>

int main() {
    int var = 20;
    int* ip;
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
Address of var variable: 0x7ffd546caa5c
Address stored in ip variable: 0x7ffd546caa5c
Value of *ip variable: 20
```

### NULL指针

&emsp;&emsp;在指针变量声明的时候，如果没有确切的地址可以为其赋值，则将指针变量赋为`NULL`，表示不指向任何东西。
&emsp;&emsp;`NULL`指针被称为`空指针`，是一个定义在标准库中值为`0`的常量：

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
ptr的地址是(nil)
```

&emsp;&emsp;如果需要检查一个指针是否为`NULL`，可以使用`if`语句：

``` cpp
if ( ptr )  /* 如果p不为NULL，则为True */
if ( !ptr ) /* 如果p为NULL，则为True   */
```