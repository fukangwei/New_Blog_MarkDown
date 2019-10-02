---
title: C字符串
categories: C语言语法详解
abbrlink: 25150
date: 2019-10-02 11:40:29
---
&emsp;&emsp;在`C`语言中，字符串实际上是使用`NUL`字符`'\0'`终止的一维字符数组。因此，一个以`NUL`结尾的字符串，包含了组成字符串的字符。
&emsp;&emsp;下面创建一个字符串`"Hello"`。由于在数组的末尾存储了空字符，所以字符数组的大小比单词`"Hello"`的字符数多一个：

``` cpp
char greeting[6] = {'H', 'e', 'l', 'l', 'o', '\0'};
```

依据数组初始化规则，可以把上面的语句写成以下语句：

``` cpp
char greeting[] = "Hello";
```

实际上不需要把`NUL`字符放在字符串常量的末尾。`C`编译器会在初始化数组时，自动把`'\0'`放在字符串的末尾。

``` cpp
#include <stdio.h>

int main() {
    char greeting[6] = { 'H', 'e', 'l', 'l', 'o', '\0' };
    printf ( "Greeting message: %s\n", greeting );
    return 0;
}
```

执行结果：

``` cpp
Greeting message: Hello
```