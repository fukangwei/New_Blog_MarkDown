---
title: getch和ungetch函数
date: 2018-12-06 13:06:07
categories: C语言语法详解
---
&emsp;&emsp;程序中往往回出现这样的情况：程序不能确定它已经读入的输入是否足够，除非超前多读入一些输入。例如，把读入一些字符以合成一个数字的情况：看到一个非数字字符之前，已经读入的数的完整性是不能确定的。

``` cpp
/* 函数getop是用来获取下一个运算符或操作数。该函数实现起来比较容易。它需要
   跳过空格与制表符。如果下一个字符不是数字或小数点，则返回；否则把这些字符
   串收集起来(其中可能包含小数点)，并返回NUMBER，以标识数已经收集起来了 */
#include <ctype.h>

int getch ( void );
void ungetch ( int );

int getop ( char s[] ) { /* 获取下一个运算符或数字操作数 */
    int i, c;

    while ( ( s[0] = c = getch() == ' ' || c == '\t' ) ) /* 跳过空格或制表符 */
        ;

    s[1] = '\0';

    if ( !isdigit ( c ) && c != '.' ) {
        return c; /* 不是数字 */
    }

    i = 0;

    if ( isdigit ( c ) ) /* 收集整数部分 */
        while ( ( isdigit ( s[i++] = c = getch() ) ) )
            ;

    if ( c == '.' ) /* 收集小数部分 */
        while ( ( isdigit ( s[i++] = c = getch() ) ) )
            ;

    s[i] = '\0';

    if ( c != EOF ) {
        ungetch ( c ); /* 反读不需要的字符 */
    }

    return NUMBER;
}
```

&emsp;&emsp;由于程序要超前读入一个字符，这样就导致最后有一个字符不属于当前所要读入的数。如果能`反读`不需要的字符，该问题就可以得到解决。每当程序多读入一个字符时，就把它压回到输入中，对代码其余部分而言就好像没有读入该字符一样。我们可以编写一对互相协作的函数来比较方便地模拟反取字符操作。
&emsp;&emsp;`getch`函数用于读取下一个待处理的字符，而`ungetch`函数则用于把字符放回到输入中。这样，此后在调用`getch`函数时，在读入新的输入之前先返回`ungetch`函数放回的那个字符。
&emsp;&emsp;`getch`和`ungetch`的协同工作原理为：`ungetch`函数是把要压回的字符放到一个共享缓冲区(字符数组)中，当该缓冲区不空时，`getch`函数就从缓冲区中读取字符；当缓冲区为空时，`getch`函数调用`getchar`函数直接从输入中读取字符。注意，这里还要增加一个下标变量来标记缓冲区中当前字符的位置。
&emsp;&emsp;由于缓冲区与下标变量是提供`getch`与`ungetch`函数共享的，并且在两次调用之前必须保持值不变，因此它们必须是这两个函数的外部变量。可以按照下列方式编写`getch`和`ungetch`函数及其共享变量：

``` cpp
#include <ctype.h>

#define BUFSIZE 100

char buf[BUFSIZE]; /* 用于ungetch函数的缓冲区 */
int bufp = 0; /* buf中下一个空闲位置 */

int getch ( void ) { /* 取一个字符(可能是压回的字符) */
    return ( bufp > 0 ) ? buf[--bufp] : getchar();
}

void ungetch ( int c ) { /* 把字符c压回到输入中 */
    if ( bufp >= BUFSIZE ) {
        printf ( "ungetch: too many charachters\n" );
    } else {
        buf[bufp++] = c;
    }
}
```

&emsp;&emsp;**补充说明**：`getch`和`ungetch`函数不是标准的库函数，因此不具有移植性。