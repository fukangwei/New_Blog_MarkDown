---
title: c_str函数
categories: C++语法详解
date: 2018-12-27 09:25:53
---
### 函数原型

&emsp;&emsp;`c_str`函数返回一个指向`C`风格字符串的指针常量，内容与原`string`相同：<!--more-->

``` cpp
const char *c_str();
```

### 注意点

&emsp;&emsp;如下代码将报出错误，因为将一个`const char *`赋于一个`char *`：

``` cpp
char *c;
string s = "1234";
c = s.c_str();
```

&emsp;&emsp;应该这样使用：

``` cpp
char c[20];
string s = "1234";
strcpy ( c, s.c_str() );
```