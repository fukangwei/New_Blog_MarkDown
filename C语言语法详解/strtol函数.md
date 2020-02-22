---
title: strtol函数
categories: C语言语法详解
date: 2018-12-13 09:59:50
---
&emsp;&emsp;`strtol`函数原型如下：<!--more-->

``` cpp
long int strtol ( const char *nptr, char **endptr, int base );
```

`strtol`会将`nptr`指向的字符串，根据参数`base`，按权转化为`long int`，然后返回这个值。
&emsp;&emsp;参数`base`的范围为`2`至`36`和`0`，它决定了字符串以被转换为整数的权值。可以被转换的合法字符依据`base`而定：

- 当`base`为`2`时，合法字符为`0、1`；
- 当`base`为`8`时，合法字符为`0、1、...、7`；
- 当`base`为`10`时，合法字符为`0、1、...、9`；
- 当`base`为`36`时，合法字符为`0、...、9、a、...、z`等，并且不区分大小写，例如`A`和`a`会都会被转化为`10`。当字符合法时，`0、...、9`依次被转换为十进制的`0`至`9`，`a、...、z`依次被转换为十进制的`10`至`35`。

&emsp;&emsp;`strtol`函数检测到第一个非法字符时，立即停止检测，其后的所有字符都会被当作非法字符处理。合法字符串会被转换为`long int`，作为函数的返回值。非法字符串，即从第一个非法字符的地址，被赋给`*endptr`。`**endptr`是个双重指针，`strtol`函数就是通过它改变`*endptr`的值，就是把第一个非法字符的地址传给`endptr`。多数情况下，`endptr`设置为`NULL`，即不返回非法字符串。
&emsp;&emsp;示例程序`1`如下：

``` cpp
char buffer[20] = "10379cend$3";
char *stop;
printf ( "%d\n", strtol ( buffer, &stop, 2 ) );
printf ( "%s\n", stop );
```

执行结果：

``` bash
2
379cend$3
```

&emsp;&emsp;示例程序`2`如下：

``` cpp
char buffer[20] = "10379cend$3";
char *stop;
printf ( "%d\n", strtol ( buffer, &stop, 8 ) );
printf ( "%s\n", stop );
```

执行结果：

``` bash
543
9cend$3
```

- 如果`base`为`0`并且字符串不是以`0x`或者`0X`开头，则按十进制进行转化；
- 如果`base`为`0`或者`16`并且字符串以`0x`或者`0X`开头，那么`x`或者`X`被忽略，字符串按`16`进制转化；
- 如果`base`不等于`0`和`16`，并且字符串以`0x`或者`0X`开头，那么`x`被视为非法字符。

&emsp;&emsp;示例程序`3`如下：

``` cpp
char buffer[20] = "0x31da6c";
char *stop;
printf ( "%d\n", strtol ( buffer, &stop, 0 ) );
printf ( "%s\n", stop );
```

执行结果为`3267180`(`stop`为空)。
&emsp;&emsp;示例程序`4`如下：

``` cpp
char buffer[20] = "0x31da6c";
char *stop;
printf ( "%d\n", strtol ( buffer, &stop, 13 ) );
printf ( "%s\n", stop );
```

执行结果：

``` bash
0
0x31da6c
```

最后需要说明的是，对于`nptr`指向的字符串，其开头和结尾处的空格被忽视，字符串中间的空格被视为非法字符。
&emsp;&emsp;示例程序`5`如下：

``` cpp
char buffer_1[20] = "10379c";
char buffer_2[20] = "      10379c        ";
char buffer_3[20] = "      10      379c        ";
printf ( "%d\n", strtol ( buffer_1, NULL, 0 ) );
printf ( "%d\n", strtol ( buffer_2, NULL, 0 ) );
printf ( "%d\n", strtol ( buffer_3, NULL, 0 ) );
```

执行结果：

``` bash
10379
10379
10
```