---
title: C语言的复合文字
date: 2018-12-10 16:01:32
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C语言的复合文字](https://blog.csdn.net/createchance/article/details/9451831)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;假如需要向一个带有int型参量的函数传递一个值，这时可以传递一个int型常量，也可以传递一个int型的变量。在C99标准之前，数组参数情况于现在不一样，没有所谓的数组常量可供传递，而在C99中增加了复合文字。文字是非符号的常量，如`5`是int型的文字，`25.3`是double型的文字。由此可见如果有能够表示数组和结构内容的文字，那么编程将会更加方便。
&emsp;&emsp;对于数组来说，复合文字看起来就像是在数组初始化列表加上圆括号括起来的类型名，例如下面是普通数组的声明方法：

``` c
int a[2] = {10, 20};
```

下面是一个复合文字，它创建了一个包含两个int型的无名称数组：

``` c
( int [2] ) { 10, 20 };
```

注意，类型名就是前面声明中去掉a的剩余部分，即`int [2]`。
&emsp;&emsp;正如初始化一个数组时可以不指定数组的大小一样，初始化一个复合文字也可以省略大小：

``` c
( int [] ) { /* 有三个元素的复合文字 */
    10, 20, 30
};
```

由于这些复合文字没有名字，不能在一个语句中创建它们，需要在其他语句中使用。其中一种方法是使用指针：

``` c
int* p;
p = ( int [] ) {
    10, 20, 30
};
```

注意这个文字常量被表示为一个int型数组。与数组名相同，这个常量表示这个数组的首地址，例如在本例中`*p == 10`。
&emsp;&emsp;另外复合文字可以作为参数传递给一个函数：

``` c
int sum ( int ar[], int n );
/* ... */
int tot;
tot = sum ( ( int [] ) { 1, 2, 3, 4 }, 4 );
```

这种给函数传递数组信息而不用先创建数组的方式，符合常量通常的使用方法。
&emsp;&emsp;这种方法也可以使用在多维数组的使用上面，如创建一个二维数组并保存其地址：

``` c
int ( *p ) [4];
p = ( int [2][4] ) {
    {1, 2, 3, 4}, {5, 6, 7, 8}
};
```

其中复合文字类型是`int [2][4]`，即一个`2*4`的int数组。综合使用代码：

``` c
#include <stdio.h>

#define COLS 4​

void sum2d ( int a[][COLS], int row );
void sum ( int a[], int n );

int main ( void ) {
    int* p1;
    int ( *p2 ) [COLS];
    p1 = ( int [2] ) {
        10, 20
    };

    p2 = ( int [2][COLS] ) {
        {1, 2, 3, 4}, {5, 6, 7, 8}
    };

    sum ( p1, 2 );
    sum2d ( p2, 2 );
}
​
void sum ( int a[], int n ) {
    int tot = 0;

    for ( int i = 0; i < n; i++ ) {
        tot += a[i];
    }

    printf ( "The sum of all is %d\n", tot );
}

void sum2d ( int a[][COLS], int row ) {
    int tot = 0;

    for ( int i = 0; i < row; i++ ) {
        for ( int j = 0; j < COLS; j++ ) {
            tot += a[i][j];
        }
    }

    printf ( "The sum of all is %d\n", tot );
}
```

编译时，要加上`--std=c99`。