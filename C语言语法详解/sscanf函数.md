---
title: sscanf函数
categories: C语言语法详解
date: 2018-12-15 18:11:10
---
&emsp;&emsp;`sscanf`是从一个字符串中读取与指定格式相符的数据：<!--more-->

``` cpp
int sscanf ( string str, string fmt, mixed var1, mixed var2 ... );
```

&emsp;&emsp;代码示例：

``` cpp
#include <stdio.h>

int main ( void ) {
    char buf[512] = {0};
    sscanf ( "123456 ", "%s", buf );
    printf ( "%s\n", buf );
    return 0;
}
```

执行结果：

``` cpp
123456
```

&emsp;&emsp;`sscanf`还可以设置更复杂的读取格式，参考{% post_link "C语言语法详解/scanf函数" %}。