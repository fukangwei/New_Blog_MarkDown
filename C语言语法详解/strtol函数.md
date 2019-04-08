---
title: strtol函数
date: 2018-12-13 09:59:50
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[strtol函数详解](https://blog.csdn.net/S031302306/article/details/52060914)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;strtol函数原型如下所示：

``` c
long int strtol ( const char *nptr, char **endptr, int base );
```

strtol会将nptr指向的字符串，根据参数base，按权转化为`long int`，然后返回这个值。
&emsp;&emsp;参数base的范围为2至36和0，它决定了字符串以被转换为整数的权值。可以被转换的合法字符依据base而定，举例来说，当base为2时，合法字符为`0、1`；base为8时，合法字符为`0、1、…、7`；base为10时，合法字符为`0、1、…、9`；base为36时，合法字符为`0、…、9、a、…、z`等，并且不区分大小写，例如`A`和`a`会都会被转化为10。当字符合法时，`0、…、9`依次被转换为十进制的0至9，`a、…、z`依次被转换为十进制的10至35。
&emsp;&emsp;strtol函数检测到第一个非法字符时，立即停止检测，其后的所有字符都会被当作非法字符处理。合法字符串会被转换为`long int`，作为函数的返回值。非法字符串，即从第一个非法字符的地址，被赋给`*endptr`。`**endptr`是个双重指针，strtol函数就是通过它改变`*endptr`的值，就是把第一个非法字符的地址传给endptr。多数情况下，endptr设置为NULL，即不返回非法字符串。
&emsp;&emsp;示例程序1如下所示：

``` c
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

&emsp;&emsp;示例程序2如下所示：

``` c
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

另外，如果base为0并且字符串不是以`0x`或者`0X`开头，则按十进制进行转化；如果base为0或者16并且字符串以`0x`或者`0X`开头，那么`x`或者`X`被忽略，字符串按16进制转化；如果base不等于0和16，并且字符串以`0x`或者`0X`开头，那么`x`被视为非法字符。示例程序3如下所示：

``` c
char buffer[20] = "0x31da6c";
char *stop;
printf ( "%d\n", strtol ( buffer, &stop, 0 ) );
printf ( "%s\n", stop );
```

执行结果为`3267180`(stop为空)。示例程序4如下所示：

``` c
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

&emsp;&emsp;最后需要说明的是，对于nptr指向的字符串，其开头和结尾处的空格被忽视，字符串中间的空格被视为非法字符。示例程序5如下所示：

``` c
char buffer_1[20] = "10379c";
char buffer_2[20] = "      10379c        ";
char buffer_3[20] = "      10      379c        ";
printf ( "%d\n", strtol ( buffer_1, NULL, 0 ) );
printf ( "%d\n", strtol ( buffer_2, NULL, 0 ) );
printf ( "%d\n", strtol ( buffer_3, NULL, 0 ) );
```

执行结果为：

``` bash
10379
10379
10
```