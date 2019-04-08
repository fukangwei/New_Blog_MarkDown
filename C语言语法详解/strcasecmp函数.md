---
title: strcasecmp函数
date: 2018-12-10 20:31:20
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C语言strncasecmp](http://c.biancheng.net/cpp/html/168.html)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;函数原型如下所示：

``` c
#include <string.h>
int strcasecmp ( const char* s1, const char* s2 );
```

strcasecmp用来比较参数s1和s2字符串，比较时会自动忽略大小写的差异。若参数s1和s2字符串相同则返回0；s1的长度大于s2的长度则返回大于0的值；s1的长度若小于s2的长度则返回小于0的值。范例如下所示：

``` c
#include <string.h>

int main ( void ) {
    char* a = "aBcDeF";
    char* b = "AbCdEf";

    if ( !strcasecmp ( a, b ) ) {
        printf ( "%s = %s\n", a, b );
    }
}
```

执行结果：

``` bash
aBcDeF = AbCdEf
```