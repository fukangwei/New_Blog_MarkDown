---
title: fractions模块
categories: Python语法
date: 2018-12-27 18:14:41
---
&emsp;&emsp;`fractions`模块提供了对分数类型的支持：<!--more-->

``` python
>>> from fractions import Fraction
>>> Fraction(16, -10)
Fraction(-8, 5)
>>> Fraction(123)
Fraction(123, 1)
>>> Fraction('3/7')
Fraction(3, 7)
>>> Fraction('-3/7')
Fraction(-3, 7)
>>> Fraction('7e-6')
Fraction(7, 1000000)
>>> Fraction(2.25)
Fraction(9, 4)
```

&emsp;&emsp;`fractions`还支持数学运算：

``` python
>>> from fractions import Fraction
>>> x = Fraction(1, 3)
>>> y = Fraction(4, 6)
>>> x + y
Fraction(1, 1)
```

&emsp;&emsp;浮点数与分数的转换：

``` python
>>> from fractions import Fraction
>>> f = 2.5
>>> z = Fraction(*f.as_integer_ratio())
>>> z
Fraction(5, 2)
>>> x = Fraction(1, 3)
>>> float(x)
0.3333333333333333
```