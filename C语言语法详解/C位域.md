---
title: C位域
date: 2019-10-02 13:54:43
categories: C语言语法详解
---
&emsp;&emsp;如果程序的结构中包含多个开关量，其值只有`TRUE`或者`FALSE`：

``` cpp
struct {
    unsigned int widthValidated;
    unsigned int heightValidated;
} status;
```

这种结构需要`8`字节的内存空间，但实际上每个变量中只存储`0`或者`1`。在这种情况下，`C`语言提供了一种更好的利用内存空间的方式。如果你在结构内使用这样的变量，则可以定义变量的宽度，用来告诉编译器，你将只使用这些字节。上面的结构体可以重写成：

``` cpp
struct {
    unsigned int widthValidated : 1;
    unsigned int heightValidated : 1;
} status;
```

此时，`status`变量将占用`4`个字节的内存空间，但是只有`2`位被用来存储值。

``` cpp
#include <stdio.h>
#include <string.h>

struct { /* 定义结构体 */
    unsigned int widthValidated;
    unsigned int heightValidated;
} status1;

struct { /* 定义位域 */
    unsigned int widthValidated : 1;
    unsigned int heightValidated : 1;
} status2;

int main() {
    printf ( "Memory size occupied by status1: %d\n", sizeof ( status1 ) );
    printf ( "Memory size occupied by status2: %d\n", sizeof ( status2 ) );
    return 0;
}
```

执行结果：

``` cpp
Memory size occupied by status1: 8
Memory size occupied by status2: 4
```

### 位域声明

&emsp;&emsp;带有预定义宽度的变量被称为`位域`，在结构内声明位域的语法如下：

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

实例
#include <stdio.h>
#include <string.h>

struct
{
  unsigned int age : 3;
} Age;

int main( )
{
   Age.age = 4;
   printf( "Sizeof( Age ) : %d\n", sizeof(Age) );
   printf( "Age.age : %d\n", Age.age );

   Age.age = 7;
   printf( "Age.age : %d\n", Age.age );

   Age.age = 8; // 二进制表示为 1000 有四位，超出
   printf( "Age.age : %d\n", Age.age );

   return 0;
}
当上面的代码被编译时，它会带有警告，当上面的代码被执行时，它会产生下列结果：

Sizeof( Age ) : 4
Age.age : 4
Age.age : 7
Age.age : 0
