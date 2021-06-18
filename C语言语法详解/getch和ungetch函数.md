---
title: getch和ungetch函数
categories: C语言语法详解
date: 2018-12-06 13:06:07
---
### 问题发现

&emsp;&emsp;程序中往往会出现这样的情况：程序不能确定它已经读入的输入数据是否足够，除非超前多读入一些输入数据。<!--more-->
&emsp;&emsp;例如，把读入一些字符以合成一个数字，看到一个非数字字符之前，已经读入的数的完整性是不能确定的。
&emsp;&emsp;由于程序要超前读入一个字符，这样就导致最后有一个字符不属于当前所要读入的数。如果能把多读入的那个字符压回到输入中，该问题就可以得到解决。

### 问题解决

&emsp;&emsp;可以编写一对互相协作的函数来解决以上问题：

1. `getch`函数用于读取下一个待处理的字符。
2. `ungetch`函数用于把字符放回到输入中。

``` cpp
#include <ctype.h>

#define BUFSIZE 100

char buf[BUFSIZE]; /* ungetch函数的缓冲区 */
int bufp = 0; /* buf中下一个空闲位置 */

int getch ( void ) { /* 取一个字符 */
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