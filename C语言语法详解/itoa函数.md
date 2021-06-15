---
title: itoa函数
categories: C语言语法详解
date: 2018-12-12 07:12:02
---
&emsp;&emsp;`itoa`的功能是将任意类型的数字转换为字符串。<!--more-->

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

&emsp;&emsp;简化版的`itoa`函数实现如下：

``` cpp
void my_itoa ( int n, char s[] ) {
    int i, sign;

    if ( ( sign = n ) < 0 ) { /* record sign */
        n = -n; /* make n positive */
    }

    i = 0;

    do { /* generate digits in reverse order */
        s[i++] = n % 10 + '0'; /* get next digit */
    } while ( ( n /= 10 ) > 0 ); /* delete it */

    if ( sign < 0 ) {
        s[i++] = '-';
    }

    s[i] = '\0';
    reverse ( s );
}
```

&emsp;&emsp;完整版的`itoa`函数实现如下：

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