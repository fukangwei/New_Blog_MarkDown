---
title: cast运算符
categories: C++语法详解
date: 2019-02-07 15:20:55
---
### static_cast

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
static_cast < type-id > ( expression )
```

该运算符把`expression`转换为`type-id`类型。

``` cpp
int i;
float f = 166.71;
i = static_cast<int> ( f ); /* i is 166 */
```