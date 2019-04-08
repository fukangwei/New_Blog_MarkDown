---
title: ctype头文件
date: 2018-12-06 17:38:46
categories: C语言语法详解
---
&emsp;&emsp;该博客从网上整理了ctype.h文件有关的内容，感谢分享资料的人们！
&emsp;&emsp;ctype.h提供了几个有用的测试和转换函数：

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

&emsp;&emsp;字符类说明如下所示：

- `Digits`: A set of whole numbers `0 1 2 3 4 5 6 7 8 9`
- `Hexadecimal digits`: This is the set of `0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f`
- `Lowercase letters`: This is a set of `a b c d e f g h i j k l m n o p q r s t u v w x y z`
- `Uppercase letters`: A set of whole numbers `A B C D E F G H I J K L M N O P Q R S T U V W X Y Z`
- `Letters`: This is a set of lowercase letters and uppercase letters
- `Alphanumeric characters`: This is a set of Digits, Lowercase letters and Uppercase letters
- `Punctuation characters`: This is a set of ``! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ ] ^ _ ` { | } ~``
- `Graphical characters`: This is a set of Alphanumeric characters and Punctuation characters
- `Space characters`: This is a set of tab, newline, vertical tab, form feed, carriage return, and space
- `Printable characters`: This is a set of Alphanumeric characters, Punctuation characters and Space characters
- `Control characters`: In ASCII, these characters have octal codes 000 through 037, and 177 (DEL)
- `Blank characters`: These are space and tab
- `Alphabetic characters`: This is a set of Lowercase letters and Uppercase letters