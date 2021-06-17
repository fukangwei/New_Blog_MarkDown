---
title: EOF详解
categories: C语言语法详解
date: 2018-12-09 09:54:08
---
&emsp;&emsp;`EOF`是`end of file`的缩写，表示`文字流`的结尾。这里的`文字流`可以是`file`，也可以是`stdin`。<!--more-->
&emsp;&emsp;下面这段代码就表示，如果不是文件结尾，就把文件的内容显示到屏幕上：

``` cpp
int c;

while ( ( c = fgetc ( fp ) ) != EOF ) {
    putchar ( c );
}
```

&emsp;&emsp;很自然地就以为，每个文件的结尾处，有一个叫做`EOF`的特殊字符。但是后来才发现，`EOF`不是特殊字符，而是一个定义在`stdio.h`中的常量，即`#define EOF (-1)`。在`Linux`系统之中，当系统读取到文件结尾时，它会返回一个信号值，也就是`EOF`。
&emsp;&emsp;上面的代码有一个问题：`fgetc`不仅是遇到文件结尾时返回`EOF`，而且当发生错误时，也会返回`EOF`。因此`C`语言又提供了`feof`函数，用来保证确实是到了文件结尾。新版本的代码如下：

``` cpp
int c;

while ( !feof ( fp ) ) {
    c = fgetc ( fp );
    /* do something; */
}
```

&emsp;&emsp;但是，这样写也有问题：`fgetc`读取文件的最后一个字符后，`feof`函数依然返回`0`，表明没有到达文件结尾；只有当`fgetc`向后再读取一个字符时，`feof`才会返回一个非零值，表示到达文件结尾。所以最保险的写法如下：

``` cpp
int c = fgetc ( fp );

while ( c != EOF ) {
    /* do something; */
    c = fgetc ( fp );
}

if ( feof ( fp ) ) {
    printf ( "\n End of file reached." );
} else {
    printf ( "\n Something went wrong." );
}
```