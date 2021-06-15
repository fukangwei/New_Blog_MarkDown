---
title: ctype头文件
categories: C语言语法详解
date: 2018-12-06 17:38:46
---
&emsp;&emsp;`ctype.h`提供了几个有用的测试和转换函数：<!--more-->

- `int isalnum(int c)`：该函数检查传递的字符是否是字母或数字。
- `int isalpha(int c)`：该函数是否传递的字符是字母。
- `int iscntrl(int c)`：该函数是否传递的字符是控制字符。
- `int isdigit(int c)`：该函数是否传递的字符是十进制数字。
- `int isgraph(int c)`：该函数是否传递的字符的图形表示，使用的语言环境。
- `int islower(int c)`：该函数检查传递的字符是否是小写字母。
- `int isprint(int c)`：该函数检查传递的字符是否是可打印的。
- `int ispunct(int c)`：该函数检查传递的字符是否是标点符号。
- `int isspace(int c)`：该函数检查传递的字符是否是空白。
- `int isupper(int c)`：该函数检查传递的字符是否是大写字母。
- `int isxdigit(int c)`：该函数检查传递的字符是否是十六进制数字。

&emsp;&emsp;该库还包含两个转换函数：

- `int tolower(int c)`：这个函数转换大写字母为小写。
- `int toupper(int c)`：这个函数小写字母转换为大写。

&emsp;&emsp;字符类说明如下：

- `Digits`：集合为`0 1 2 3 4 5 6 7 8 9`。
- `Hexadecimal digits`：集合为`0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f`。
- `Lowercase letters`：集合为`a b c d e f g h i j k l m n o p q r s t u v w x y z`。
- `Uppercase letters`：集合为`A B C D E F G H I J K L M N O P Q R S T U V W X Y Z`。
- `Alphabetic characters`：`Lowercase letters`和`Uppercase letters`的集合。
- `Alphanumeric characters`：`Digits`、`Lowercase letters`和`Uppercase letters`的集合。
- `Punctuation characters`：集合为``! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ ] ^ _ ` { | } ~``。
- `Graphical characters`：`Alphanumeric characters`和`Punctuation characters`的集合。
- `Space characters`：空格、换行、`TAB`等空白字符。
- `Printable characters`：`Alphanumeric characters`、`Punctuation characters`和`Space characters`的集合。
- `Control characters`：`ASCII`表中的控制字符。