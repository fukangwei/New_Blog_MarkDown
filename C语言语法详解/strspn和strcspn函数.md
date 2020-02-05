---
title: strspn和strcspn函数
categories: C语言语法详解
abbrlink: b30a0322
date: 2018-12-13 13:25:52
---
### strcspn函数

&emsp;&emsp;函数原型为：<!--more-->

``` c
#include <string.h>
size_t strcspn ( const char* s, const char* reject );
```

`strcspn`从参数`s`字符串的开头计算连续的字符，而这些字符都完全不在参数`reject`所指的字符串中。简单地说，若`strcspn`返回的数值为`n`，则代表字符串`s`开头连续有`n`个字符都不含字符串`reject`内的字符。该函数返回字符串`s`开头连续不含字符串`reject`内的字符数目。

``` cpp
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char* str = "Linux was first developed for 386/486-based pcs.";
    printf ( "%ld\n", strcspn ( str, " " ) );
    printf ( "%ld\n", strcspn ( str, "/-" ) );
    printf ( "%ld\n", strcspn ( str, "1234567890" ) );
}
```

执行结果：

``` bash
5 /* 只计算到出现空格，所以返回“Linux”的长度 */
33 /* 计算到出现“/”或“－”，所以返回到“6”的长度 */
30 /* 计算到出现数字字符为止，所以返回“3”出现前的长度 */
```

### strspn函数

&emsp;&emsp;函数原型为：

``` cpp
#include <string.h>
size_t strspn ( const char* s, const char* accept );
```

`strspn`从参数`s`字符串的开头计算连续的字符，而这些字符都完全是`accept`所指字符串中的字符。简单的说，若`strspn`返回的数值为`n`，则代表字符串`s`开头连续有`n`个字符都是属于字符串`accept`内的字符。该函数返回字符串`s`开头连续包含字符串`accept`内的字符数目。

``` cpp
#include <string.h>
#include <stdio.h>

int main() {
    char* str = "Linux was first developed for 386/486-based pcs.";
    printf ( "%ld\n", strspn ( str, "Linux" ) );
    printf ( "%ld\n", strspn ( str, "/-" ) );
    printf ( "%ld\n", strspn ( str, "1234567890" ) );
}
```

执行结果：

``` bash
5 /* 包含“linux”字符 */
0 /* 开始不包含 */
0 /* 开始不包含 */
```

其函数实现如下：

``` cpp
int mystrspn ( const char* s, const char* accept ) {
    const char* p = NULL;
    const char* a = NULL;
    int count = 0;​

    for ( p = s; *p != '\0'; p++ ) {
        for ( a = accept; *a != '\0'; a++ ) {
            if ( *p == *a ) {
                break;
            }
        }

        if ( *a == '\0' ) {
            return count;
        }

        ++count;
    }

    return count;
}
```