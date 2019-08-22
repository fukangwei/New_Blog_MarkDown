---
title: c_str函数
date: 2018-12-27 09:25:53
categories: C++语法详解
---
&emsp;&emsp;函数原型为：

``` cpp
const char *c_str();
```

`c_str`函数返回一个指向正规`C`字符串的指针常量，内容与本`string`串相同。这是为了与`C`语言兼容，必须通过`string`类对象的成员函数`c_str`把`string`对象转换成`C`语言中的字符串样式。
&emsp;&emsp;注意，一定要使用`strcpy`等函数来操作`c_str`返回的指针，比如最好不要这样写：

``` cpp
char *c;
string s = "1234";
/* c最后指向的内容是无用的，因为s对象被析构，其内容被处理，
   同时编译器也将报错：将一个“const char *”赋于一个“char *” */
c = s.c_str();
```

应该这样使用：

``` cpp
char c[20];
string s = "1234";
strcpy ( c, s.c_str() );
```

这样才不会出错，`c_str`返回的是一个临时指针，不能对其进行操作。
&emsp;&emsp;如果一个函数要求`char *`参数，可以使用`c_str`进行处理：

``` cpp
string s = "Hello World!";
printf ( "%s", s.c_str() ); /* 输出 "Hello World!" */
```