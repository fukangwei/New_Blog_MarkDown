---
title: decimal模块
categories: Python语法
date: 2018-12-27 17:38:32
---
&emsp;&emsp;`decimal`模块支持快速正确舍入的十进制浮点运算。它相对于`float`数据类型提供了如下几个优点：<!--more-->

- 可以精确地表示十进制数。诸如`1.1`和`2.2`等数字在计算机中没有精确的二进制表示。
- 算数的结果是精确的。在十进制中，`0.1 + 0.1 + 0.1 - 0.3`正好等于零；在二进制中，结果为`5.5511151231257827e-017`。
- 与基于硬件的二进制浮点不同，十进制模块具有用户可改变的精度：

``` python
>>> from decimal import *
>>> getcontext().prec = 6
>>> Decimal(1) / Decimal(7)
Decimal('0.142857')
>>> getcontext().prec = 28
>>> Decimal(1) / Decimal(7)
Decimal('0.1428571428571428571428571429')
```

&emsp;&emsp;`decimal`基本用法如下：

``` python
>>> from decimal import *
>>> getcontext().prec = 28
>>> Decimal(10)
Decimal('10')
>>> Decimal('3.14')
Decimal('3.14')
>>> Decimal(3.14)
Decimal('3.140000000000000124344978758017532527446746826171875')
>>> Decimal(2) ** Decimal('0.5')
Decimal('1.414213562373095048801688724')
>>> Decimal('NaN')
Decimal('NaN')
>>> Decimal('-Infinity')
Decimal('-Infinity')
```