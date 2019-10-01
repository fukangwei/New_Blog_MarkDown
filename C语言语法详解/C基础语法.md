---
title: C基础语法
date: 2019-10-01 20:37:14
categories: C语言语法详解
---
### Hello, World

&emsp;&emsp;`C`语言是一种通用的、面向过程式的计算机程序设计语言。最简单的示例如下(`hello.c`)：

``` cpp
#include <stdio.h>

int main() {
    /* 我的第一个C程序 */
    printf("Hello, World!\n");
    return 0;
}
```

- 所有的`C`语言程序都需要包含`main`函数，代码从`main`函数开始执行。
- `/* ... */`用于注释说明。
- `printf`用于格式化输出到屏幕，该函数在头文件`stdio.h`中声明。
- `stdio.h`是一个头文件(标准输入输出头文件)，`#include`是一个预处理命令，用来引入头文件。当编译器遇到`printf`函数时，如果没有找到`stdio.h`头文件，会发生编译错误。
- `return 0;`用于表示退出程序。

### 分号;

&emsp;&emsp;在`C`程序中，分号`;`是语句结束符，每个语句必须以分号结束。例如，下面是两个不同的语句：

``` cpp
printf("Hello, World!\n");
return 0;
```

### 标识符

&emsp;&emsp;`C`标识符是用来标识变量、函数或任何其他用户自定义项目的名称。一个标识符以字母`A-Z`或`a-z`或下划线`_`开始，后跟`0`个或`多个`字母、下划线和数字`0-9`。
&emsp;&emsp;`C`标识符内不允许出现标点字符，比如`@`、`$`和`%`。`C`是区分大小写的编程语言，因此`Manpower`和`manpower`是两个不同的标识符。下面列出几个有效的标识符：

``` cpp
mohd      zara   abc  move_name  a_123
myname50  _temp  j    a23b9      retVal
```