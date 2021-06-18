---
title: ctype头文件
categories: C语言语法详解
date: 2018-12-06 17:38:46
---
&emsp;&emsp;`ctype.h`提供了几个有用的测试和转换函数。<!--more-->

### 测试函数

- `int isalnum(int c)`：检查`c`是否为字母或数字。
- `int isalpha(int c)`：检查`c`是否为字母。
- `int iscntrl(int c)`：检查`c`是否为控制字符。
- `int isdigit(int c)`：检查`c`是否为十进制数字。
- `int islower(int c)`：检查`c`是否为小写字母。
- `int isprint(int c)`：检查`c`是否为可打印的。
- `int ispunct(int c)`：检查`c`是否为标点符号。
- `int isspace(int c)`：检查`c`是否为空白。
- `int isupper(int c)`：检查`c`是否为大写字母。
- `int isxdigit(int c)`：检查`c`是否为十六进制数字。

### 转换函数

- `int tolower(int c)`：转换`c`为小写字母。
- `int toupper(int c)`：转换`c`为大写字母。