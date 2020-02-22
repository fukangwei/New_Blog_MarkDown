---
title: strchr、strstr和strrstr函数
categories: C语言语法详解
date: 2018-12-14 21:56:15
---
### strchr函数

&emsp;&emsp;`strchr`用来查找某字符在字符串中首次出现的位置：<!--more-->

``` cpp
#include <string.h>
char* strchr ( const char* str, int c );
```

参数`str`为要查找的字符串，`c`为要查找的字符。如果找到指定的字符，则返回该字符所在地址，否则返回`NULL`。
&emsp;&emsp;`strchr`将会找出`str`字符串中第一次出现字符`c`的地址，然后将该地址返回。注意，字符串`str`的结束标志`NUL`也会被纳入检索范围，所以`str`的最后一个字符也可以被定位。如果希望查找某字符在字符串中最后一次出现的位置，可以使用`strrchr`函数。还有一种格式，字符是以`char`型给出的：

``` cpp
char* strchr ( const char* string, char c );
```

&emsp;&emsp;查找字符`5`首次出现的位置：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    char* s = "0123456789012345678901234567890";
    char* p;
    p = strchr ( s, '5' );
    printf ( "%ld\n", s );
    printf ( "%ld\n", p );
    return 0;
}
```

执行结果：

``` bash
12016464
12016469
```

其函数实现为：

``` cpp
char* mystrchr ( char* s, char c ) {
    while ( *s != '\0' && *s != c ) {
        ++s;
    }

    return *s == c ? s : NULL;
}
```

### strstr函数

&emsp;&emsp;函数功能为在一个字符串中查找一个字串：

``` cpp
char* strstr ( char const* s1, char const* s2 );
```

`strstr`实现在`s1`中查找整个`s2`第一次出现的位置，并返回一个指向该位置的指针。如果`s2`并没有完整的出现在`s1`的任何地方，函数返回一个`NULL`指针；如果第二个字符串是一个空字符串，函数返回`s1`。

``` cpp
#include <stdio.h>
#include <string.h>

char* mystrstr ( char* s1, char* s2 ) {
    char* pChar;
    pChar = s2;
    int len = strlen ( s2 );

    for ( ; strchr ( s1, *pChar ) != '\0'; s1++ ) {
        if ( strncmp ( s1, pChar, len ) == 0 ) {
            return s1;
        }
    }

    return NULL;
}

int main() {
    char str1[20] = "abcdhello!";
    char str2[20] = "cd";
    char* p = mystrstr ( str1, str2 );
    printf ( "%s\n", p );
    return 0;
}
```

### strrstr函数

&emsp;&emsp;函数原型如下：

``` cpp
char* strrstr ( char* haystack, char* needle );
```

函数功能为从字符串`haystack`中寻找`needle`最后一次出现的位置(不比较结束符`NUL`)。函数返回指向最后一次出现`needle`位置的指针，如果没找到则返回`NULL`。

``` cpp
#include "string.h"

char* my_strrstr ( char const* s1, char const* s2 ) {
    register char* last;
    register char* current;
    last = NULL; /* 把指针初始化为我们已近找到的前一次匹配位置 */

    if ( *s2 != '\0' ) { /* 只在第2个字符串不为空时才进行查找，如果s2为空，返回NULL */
        current = strstr ( s1, s2 ); /* 查找s2在s1中第一次出现的位置 */

        /* 每次找到字符串时，让指针指向它的起始位置，然后查找该字符串下一个匹配位置 */
        while ( current != NULL ) {
            last = current;
            current = strstr ( last + 1, s2 );
        }
    }

    return last; /* 返回指向我们找到的最后一次匹配的起始位置的值 */
}
```