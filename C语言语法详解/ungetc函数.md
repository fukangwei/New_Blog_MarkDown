---
title: ungetc函数
date: 2018-12-06 13:45:47
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C语言ungetc函数：把字符退回到输入流](http://c.biancheng.net/cpp/html/269.html)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;ungetc的功能是把一个字符退回到输入流中，其函数原型为：

``` c
int ungetc ( char c, FILE* stream );
```

其中c是要写入的字符，stream是文件流指针。若函数返回字符c，表示操作成功，返回EOF表示操作失败。

``` c
#include <stdio.h>
#include <ctype.h>

void main ( void ) {
    int ch;
    int result = 0;
    printf ( "Enter an integer: " );

    while ( ( ( ch = getchar() ) != EOF ) && isdigit ( ch ) ) {
        result = result * 10 + ch - '0';
    }

    if ( ch != EOF ) {
        ungetc ( ch, stdin );
    }

    printf ( "Number = %d\nNextcharacter in stream = '%c'", result, getchar() );
}
```

执行结果：

``` bash
Enter an integer: 521a
Number = 521
Nextcharacter in stream = 'a'
```