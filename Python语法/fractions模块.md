---
title: fractions模块
categories: Python语法
date: 2018-12-27 18:14:41
---
&emsp;&emsp;`fractions`模块提供了分数类型的支持，构造函数如下：<!--more-->

``` python
class fractions.Fraction(numerator=0, denominator=1)
class fractions.Fraction(int | float | str | Decimal | Fraction)
```

可以同时提供分子(`numerator`)和分母(`denominator`)给构造函数，用于实例化`Fraction`类，但两者必须同时是`int`类型或者`numbers.Rational`类型，否则会抛出类型错误。当分母为`0`时，初始化会导致抛出`ZeroDivisionError`。

``` python
>>> from fractions import Fraction
>>> Fraction(16, -10)
Fraction(-8, 5)
>>> Fraction(123)
Fraction(123, 1)
>>> Fraction()
Fraction(0, 1)
>>> Fraction('3/7')
Fraction(3, 7)
>>> Fraction('-3/7')
Fraction(-3, 7)
>>> Fraction('1.414213\t\n')
Fraction(1414213, 1000000)
>>> Fraction('-.125')
Fraction(-1, 8)
>>> Fraction('7e-6')
Fraction(7, 1000000)
>>> Fraction(2.25)
Fraction(9, 4)
>>> Fraction(1.1)
Fraction(2476979795053773, 2251799813685248)
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