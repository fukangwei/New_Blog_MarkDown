---
title: ungetc函数
categories: C语言语法详解
date: 2018-12-06 13:45:47
---
&emsp;&emsp;`ungetc`的功能是把一个字符退回到输入流中：<!--more-->

``` cpp
int ungetc ( char c, FILE* stream );
```

其中`c`是要退回到输入流的字符，`stream`是文件流指针。若函数返回字符`c`，表示操作成功；若返回`EOF`，则表示操作失败。

``` cpp
#include <stdio.h>
#include <ctype.h>

int main ( void ) {
    int ch;
    int result = 0;
    printf ( "Enter an integer: " );

    while ( ( ( ch = getchar() ) != EOF ) && isdigit ( ch ) ) {
        result = result * 10 + ch - '0';
    }

    if ( ch != EOF ) {
        ungetc ( ch, stdin );
    }

    printf ( "Number = %d\nNext character in stream = %c\n", result, getchar() );
    return 0;
}
```

执行结果：

``` cpp
Enter an integer: 512a
Number = 512
Next character in stream = a
```