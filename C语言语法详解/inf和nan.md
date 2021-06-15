---
title: inf和nan
categories: C语言语法详解
date: 2018-12-10 21:01:10
---
&emsp;&emsp;`inf`一般是因为得到的数值超出浮点数的表示范围。<!--more-->
&emsp;&emsp;`nan`一般是因为对浮点数进行了未定义的操作，如对`-1`开方。
&emsp;&emsp;下面这几个宏用于判断一个表达式的结果是否为`inf`、`nan`：

``` cpp
#include "math.h"
int isnan(x);
int isinf(x);
```