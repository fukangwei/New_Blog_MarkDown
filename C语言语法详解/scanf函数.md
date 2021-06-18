---
title: scanf函数
categories: C语言语法详解
date: 2018-12-12 13:29:39
---
### 基本语法

&emsp;&emsp;`scanf`是`C`语言中的一个输入函数，它按用户指定的格式从键盘上把数据输入到指定的变量中：<!--more-->

``` cpp
int scanf ( const char* restrict format, ... );
```

- 若函数执行成功，则返回读入的数据项数。
- 若读到文件末尾或读取出错，则返回`EOF`。

&emsp;&emsp;举个例子：

``` cpp
int a, b;
scanf ( "%d %d", &a, &b );
```

- 如果`a`和`b`都被成功读入，则返回值为`2`。
- 如果只有`a`被成功读入，则返回值为`1`。
- 如果`a`和`b`都未被成功读入，则返回值为`0`。
- 如果遇到错误或读到文件末尾，则返回值为`EOF`。

&emsp;&emsp;如果`scanf`中的`%d`是连着写的，例如`%d%d%d`，则在输入数据时，数据之间不可以用逗号分隔，只能用空白字符分隔。若是`%d,%d,%d`，则在输入数据时，需要加`,`进行分隔，例如`2,3,4`。

### 读取浮点数

&emsp;&emsp;输入`float`型数据：

``` cpp
#include <stdio.h>

int main ( void ) {
    float a;
    scanf ( "%f", &a );
    printf ( "%f\n", a );
    return 0;
}
```

&emsp;&emsp;输入`double`型数据：

``` cpp
#include <stdio.h>

int main ( void ) {
    double a;
    scanf ( "%lf", &a );
    printf ( "%f\n", a );
    return 0;
}
```

### 读取字符串

&emsp;&emsp;输入字符串：

``` cpp
#include <stdio.h>

int main ( void ) {
    char str[50] = {0};
    scanf ( "%s", str );
    printf ( "%s\n", str );
    return 0;
}
```

### 扫描集

&emsp;&emsp;`scanf`具有一种称为`扫描集`的特性。使用扫描集时，`scanf`连续吃进集合中的字符，并放入对应的字符数组，直到发现不在集合中的字符为止。返回时，数组中具有以`NUL`结尾、由读入字符组成的字符串。
&emsp;&emsp;例如，以下的扫描集使`scanf`读取范围是大写字母`A`、`B`和`C`：

``` cpp
%[ABC]
```

&emsp;&emsp;使用连字符可以说明一个范围，例如以下扫描集使`scanf`读取的范围是大写字母`A`到`Z`：

``` cpp
%[A-Z]
```

&emsp;&emsp;用字符`^`可以说明补集，把`^`放在扫描集的第一字符时，`scanf`则只读取该扫描集的补集。
&emsp;&emsp;例如，以下的扫描集使`scanf`读取范围是除大写字母`A`、`B`和`C`之外的其他任何字符：

``` cpp
%[^ABC]
```

### 限制读取数量

&emsp;&emsp;`%n`可以限制`scanf`的读取字符的数量。如下代码限制`scanf`只能读取`3`个字符：

``` cpp
char str[50] = {0};
scanf ( "%3s", str );
```

&emsp;&emsp;`%n[a-z]`表示读入最多`n`个字符，如果遇到非小写字母的字符，则停止读取。

### 星号

&emsp;&emsp;`%*`表示跳过不读。例如输入`hello, world`，仅保留`world`：

``` cpp
#include <stdio.h>

int main ( void ) {
    char buf[50] = {0};
    scanf ( "%*s%s", buf );
    printf ( "%s\n", buf );
    return 0;
}
```

`%*s`表示第一个匹配到的`%s`被过滤掉，即`hello,`被过滤了。
&emsp;&emsp;对于`scanf("%*[^\n]");`，可以把输入缓冲区中下一个回车符之前的所有内容清理掉。
&emsp;&emsp;对于`scanf("%*c");`，可以把输入缓冲区中的下一个回车符清理掉。

### 读取有空格字符串

&emsp;&emsp;对于输入`I love you!`，如果使用`char str[80]; scanf("%s", str);`，则只能输出`I`，因为`scanf`扫描到`I`后面的空格时，就认为对`str`的扫描结束，并忽略后面的`(空格)love you!`。
&emsp;&emsp;如果要读取完整的`I love you!`，可以使用下面的程序：

``` cpp
#include <stdio.h>

int main ( void ) {
    char str[50];
    scanf ( "%[^\n]%*c", str );
    printf ( "%s\n", str );
    return 0;
}
```