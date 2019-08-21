---
title: ungetc函数
date: 2018-12-06 13:45:47
categories: C语言语法详解
---
&emsp;&emsp;`ungetc`的功能是把一个字符退回到输入流中：

``` cpp
int ungetc ( char c, FILE* stream );
```

其中`c`是要写入的字符，`stream`是文件流指针。若函数返回字符`c`，表示操作成功，返回`EOF`表示操作失败。

``` cpp
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