---
title: itoa函数
date: 2018-12-12 07:12:02
categories: C语言语法详解
---
&emsp;&emsp;`itoa`是广泛应用的非标准`C/C++`语言扩展函数。由于它不是标准`C/C++`语言函数，所以不能在所有的编译器中使用。但是，大多数的编译器通常在`stdlib.h`或`cstdlib`头文件中包含这个函数，其功能为将任意类型的数字转换为字符串。在`stdlib.h`中与之有相反功能的函数是`atoi`。函数原型为：

``` cpp
char* itoa ( int value, char* string, int radix );
```

参数`value`为被转换的整数，`string`为转换后储存的字符数组，`radix`为转换进制数，如`2、8、10、16`进制等。

``` cpp
#include <stdlib.h>
#include <stdio.h>

int main ( void ) {
    int number = 51;
    char string[25] = {0};
    itoa ( number, string, 2 );
    printf ( "integer = %d, string = %s\n", number, string );
    return 0;
}
```

&emsp;&emsp;实现`itoa`函数的源代码如下：

``` cpp
char* my_itoa ( int num, char* str, int radix ) {
    char index[] = "0123456789ABCDEF"; /* 索引表 */
    unsigned unum; /* 中间变量 */
    int i = 0, j, k;

    /* 确定unum的值 */
    if ( radix == 10 && num < 0 ) { /* 十进制负数 */
        unum = ( unsigned ) -num;
        str[i++] = '-';
    } else {
        unum = ( unsigned ) num; /* 其他情况 */
    }

    do { /* 转换 */
        str[i++] = index[unum % ( unsigned ) radix];
        unum /= radix;
    } while ( unum );

    str[i] = '\0';

    /* 逆序 */
    if ( str[0] == '-' ) {
        k = 1; /* 十进制负数 */
    } else {
        k = 0;
    }

    char temp;

    for ( j = k; j <= ( i - 1 ) / 2; j++ ) {
        temp = str[j];
        str[j] = str[i - 1 + k - j];
        str[i - 1 + k - j] = temp;
    }

    return str;
}
```