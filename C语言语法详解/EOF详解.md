---
title: EOF详解
categories: C语言语法详解
date: 2018-12-09 09:54:08
---
&emsp;&emsp;`EOF`是`end of file`的缩写，表示`文字流`(`stream`)的结尾。这里的`文字流`可以是文件(`file`)，也可以是标准输入(`stdin`)。例如下面这段代码就表示，如果不是文件结尾，就把文件的内容复制到屏幕上：<!--more-->

``` cpp
int c;

while ( ( c = fgetc ( fp ) ) != EOF ) {
    putchar ( c );
}
```

&emsp;&emsp;很自然地就以为，每个文件的结尾处，有一个叫做`EOF`的特殊字符，读取到这个字符，操作系统就认为文件结束了。但是后来才发现，`EOF`不是特殊字符，而是一个定义在头文件`stdio.h`的常量，一般等于`-1`，即`#define EOF (-1)`。
&emsp;&emsp;于是就困惑了：如果`EOF`是一个特殊字符，那么假定每个文本文件的结尾都有一个`EOF`，还是可以做到的，因为文本对应的`ASCII`码都是正值，不可能有负值。但是二进制文件怎么办呢？怎么处理文件内部包含的`-1`呢？后来查资料才知道，在`Linux`系统之中，`EOF`根本不是一个字符，而是当系统读取到文件结尾，所返回的一个信号值(也就是`-1`)。至于系统怎么知道文件的结尾，资料上说是通过比较文件的长度。所以处理文件可以写成如下代码：

``` cpp
int c;

while ( ( c = fgetc ( fp ) ) != EOF ) {
    /* do something */
}
```

这样写有一个问题：`fgetc`不仅是遇到文件结尾时返回`EOF`，而且当发生错误时，也会返回`EOF`。因此`C`语言又提供了`feof`函数，用来保证确实是到了文件结尾。上面的代码`feof`版本的写法如下：

``` cpp
int c;

while ( !feof ( fp ) ) {
    c = fgetc ( fp );
    /* do something; */
}
```

但是这样写也有问题：`fgetc`读取文件的最后一个字符以后，`C`语言的`feof`函数依然返回`0`，表明没有到达文件结尾；只有当`fgetc`向后再读取一个字符(即越过最后一个字符)，`feof`才会返回一个非零值，表示到达文件结尾。因此按照上面的写法，如果一个文件含有`n`个字符，那么`while`循环的内部操作会运行`n + 1`次，所以最保险的写法如下：

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

除了表示文件结尾，`EOF`还可以表示标准输入的结尾：

``` cpp
int c;

while ( ( c = getchar() ) != EOF ) {
    putchar ( c );
}
```

但是标准输入与文件不一样，无法事先知道输入的长度，必须手动输入一个字符，表示到达`EOF`。`Linux`中，在新的一行的开头，按下`Ctrl-D`就代表`EOF`(如果在一行的中间按下`Ctrl-D`，则表示输出`标准输入`的缓存区，所以这时必须按两次`Ctrl-D`)；`Windows`中，先回车再输入`Ctrl-Z`表示`EOF`(顺便提一句，`Linux`中按下`Ctrl-Z`表示将该进程中断，在后台挂起，用`fg`命令可以重新切回到前台；按下`Ctrl-C`表示终止该进程)。
&emsp;&emsp;那么如果真的想输入`Ctrl-D`怎么办？这时必须先按下`Ctrl-V`，然后就可以输入`Ctrl-D`，系统就不会认为这是`EOF`信号。`Ctrl-V`表示按`字面含义`解读下一个输入，要是想按`字面含义`输入`Ctrl-V`，连续输入两次就行了。