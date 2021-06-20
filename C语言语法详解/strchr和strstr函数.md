---
title: strchr和strstr函数
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
&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <string.h>

int main() {
    char s[] = "01234567890123456789";
    char* p = NULL;
    p = strchr ( s, '5' );
    printf ( "%ld\n", p - s ); /* 输出5 */
    return 0;
}
```

&emsp;&emsp;函数实现如下：

``` cpp
char* mystrchr ( char* s, char c ) {
    while ( *s != '\0' && *s != c ) {
        ++s;
    }

    return *s == c ? s : NULL;
}
```

### strstr函数

&emsp;&emsp;`strstr`用来在一个字符串中查找一个子串：

``` cpp
#include <string.h>
char* strstr ( char const* s1, char const* s2 );
```

`strstr`函数在`s1`中查找`s2`第一次出现的位置，并返回一个指向该位置的指针。如果`s2`没有出现在`s1`中，则函数返回`NULL`。
&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <string.h>

int main() {
    char str1[20] = "abcdhello!";
    char str2[20] = "cd";
    char* p = strstr ( str1, str2 );
    printf ( "%ld\n", p - str1 ); /* 输出2 */
    return 0;
}
```

&emsp;&emsp;函数实现如下：

``` cpp
char* mystrstr ( char* s1, char* s2 ) {
    char* pChar;
    pChar = s2;
    int len = strlen ( s2 );

    for ( ; strchr ( s1, *pChar ) != NULL; s1++ ) {
        if ( strncmp ( s1, pChar, len ) == 0 ) {
            return s1;
        }
    }

    return NULL;
}
```