---
title: strspn和strcspn函数
categories: C语言语法详解
date: 2018-12-13 13:25:52
---
### strcspn函数

&emsp;&emsp;函数原型为：<!--more-->

``` cpp
#include <string.h>
size_t strcspn ( const char* s, const char* reject );
```

该函数从字符串`s`的开头开始检索，如果出现属于`reject`中的某个字符，则停止检索，并返回已经检索的字符数量。

``` cpp
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char str[] = "Linux was first developed for 386/486-based pcs.";
    printf ( "%ld\n", strcspn ( str, " " ) );
    printf ( "%ld\n", strcspn ( str, "/-" ) );
    printf ( "%ld\n", strcspn ( str, "1234567890" ) );
}
```

执行结果：

``` bash
5 /* 检索到出现空格，所以返回“Linux”的长度 */
33 /* 检索到出现“/”或“-”，所以返回到“386”的“6”的长度 */
30 /* 检索到出现数字字符，所以返回“3”出现前的长度 */
```

### strspn函数

&emsp;&emsp;函数原型为：

``` cpp
#include <string.h>
size_t strspn ( const char* s, const char* accept );
```

该函数从字符串`s`的开头开始检索，如果出现不属于`accept`中的某个字符，则停止检索，并返回已经检索的字符数量。

``` cpp
#include <string.h>
#include <stdio.h>

int main() {
    char str[] = "Linux was first developed for 386/486-based pcs.";
    printf ( "%ld\n", strspn ( str, "Linux" ) ); /* 输出5 */
    printf ( "%ld\n", strspn ( str, "/-" ) ); /* 输出0 */
    printf ( "%ld\n", strspn ( str, "1234567890" ) ); /* 输出0 */
}
```

### 函数实现

&emsp;&emsp;`strspn`的函数实现如下：

``` cpp
int my_strspn ( const char* s, const char* accept ) {
    const char* p = NULL;
    const char* a = NULL;
    int count = 0;

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