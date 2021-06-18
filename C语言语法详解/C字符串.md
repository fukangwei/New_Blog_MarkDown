---
title: C字符串
categories: C语言语法详解
date: 2019-10-02 11:40:29
---
### 字符串

&emsp;&emsp;在`C`语言中，字符串实际上是使用`NUL`字符`'\0'`结尾的一维字符数组。<!--more-->
&emsp;&emsp;下面创建一个字符串`"Hello"`，由于在数组的末尾存储了`NUL`，所以字符数组的大小比单词`"Hello"`的字符数多`1`：

``` cpp
char greeting[6] = {'H', 'e', 'l', 'l', 'o', '\0'};
```

根据数组初始化规则，可以把上面的语句写成如下方式：

``` cpp
char greeting[] = "Hello";
```

`C`编译器在初始化字符串时，会自动地把`NUL`放在字符串的末尾。

### 代码实例

&emsp;&emsp;代码实例如下：

``` cpp
#include <stdio.h>

int main() {
    char greeting_1[6] = { 'H', 'e', 'l', 'l', 'o', '\0' };
    char greeting_2[] = "Hello";
    printf ( "Greeting_1 message: %s\n", greeting_1 );
    printf ( "Greeting_2 message: %s\n", greeting_2 );
    return 0;
}
```

执行结果：

``` cpp
Greeting_1 message: Hello
Greeting_2 message: Hello
```