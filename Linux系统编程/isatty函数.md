---
title: isatty函数
date: 2018-12-29 14:29:33
categories: Linux系统编程
---
&emsp;&emsp;`isatty`主要功能是检查设备类型，判断文件描述词是否是为终端机：

``` c
#include <unistd.h>
int isatty ( int desc );
```

如果参数`desc`所代表的文件描述符为一终端机则返回`1`，否则返回`0`。

``` c
#include <stdio.h>
#include <io.h>
​
int main ( void ) {
    int handle;
    handle = fileno ( stdout );
​
    if ( isatty ( handle ) ) {
        printf ( "Handle %d is a device type\n", handle );
    } else {
        printf ( "Handle %d isn't a device type\n", handle );
    }
​
    return 0;
}
```