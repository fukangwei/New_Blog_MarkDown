---
title: strtok和strsep函数
categories: C语言语法详解
date: 2018-12-14 19:55:28
---
### strtok函数

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <string.h>
char* strtok ( char* s, const char* delim );
```

`strtok`的功能是分割字符串，`s`是源字符串，`delim`为分隔符字符串。
&emsp;&emsp;`strtok`会改变源字符串，想要避免，可以使用借助`strdup`。

``` cpp
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char s[] = "hello, world!";
    char delim[] = " ,!";
    char* token = NULL;

    for ( token = strtok ( s, delim ); token != NULL; token = strtok ( NULL, delim ) ) {
        printf ( "%s\n", token );
    }

    return 0;
}
```

执行结果：

``` bash
hello
world
```

### strsep函数

&emsp;&emsp;函数原型如下：

``` cpp
#include <string.h>
char* strsep ( char** s, const char* delim );
```

`strsep`的功能是分割字符串，`s`是源字符串，`delim`为分隔符字符串。
&emsp;&emsp;`strsep`会改变源字符串，想要避免，可以使用借助`strdup`。

``` cpp
#include <string.h>
#include <stdio.h>

int main ( int arg, const char* argv[] ) {
    char str[] = "/home/fkw/project:/home/fkw:/home/someone";
    char* string = strdup ( str );
    char* p = NULL;

    while ( ( p = strsep ( &string, ":" ) ) != NULL ) {
        printf ( "%s\n", p );
    }

    return 0;
}
```

执行结果：

``` bash
/home/fkw/project
/home/fkw
/home/someone
```