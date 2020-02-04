---
title: C位域
categories: C语言语法详解
abbrlink: 14362
date: 2019-10-02 13:54:43
---
### 位域声明

&emsp;&emsp;带有预定义宽度的变量被称为`位域`，在结构内声明位域的语法如下：<!--more-->

``` cpp
struct {
    type member_name : width;
};
```

元素          | 描述
--------------|----
`type`        | 整数类型，决定了如何解释位域的值。类型可以是整型、有符号整型、无符号整型
`member_name` | 位域的名称
`width`       | 位域中位的宽度。宽度必须小于或等于指定类型的位宽度

如果需要一个变量来存储从`0`到`7`的值，可以定义一个宽度为`3`位的位域：

``` cpp
struct {
    unsigned int age : 3;
} Age;
```

上面的结构体指示`C`编译器，`age`变量将只使用`3`位来存储这个值。

``` cpp
#include <stdio.h>

struct {
    unsigned int age : 3;
} Age;

int main() {
    Age.age = 4;
    printf ( "Sizeof( Age ) : %d\n", sizeof ( Age ) );
    printf ( "Age.age : %d\n", Age.age );
    Age.age = 7;
    printf ( "Age.age : %d\n", Age.age );
    Age.age = 8; /* 8的二进制表示为“1000”，有四位，超出范围 */
    printf ( "Age.age : %d\n", Age.age );
    return 0;
}
```

当上面的代码被编译时，它会带有警告。执行结果如下：

``` cpp
Sizeof( Age ) : 4
Age.age : 4
Age.age : 7
Age.age : 0
```