---
title: decimal模块
categories: Python语法
date: 2018-12-27 17:38:32
---
&emsp;&emsp;`decimal`模块支持正确的十进制浮点运算：<!--more-->

``` python
>>> from decimal import *
>>> getcontext().prec = 28  # 设置精度
>>> Decimal(10)
Decimal('10')
>>> Decimal('3.14')
Decimal('3.14')
>>> Decimal(3.14)
Decimal('3.140000000000000124344978758017532527446746826171875')
>>> Decimal(1) / Decimal(7)
Decimal('0.1428571428571428571428571429')
>>> Decimal(2) ** Decimal('0.5')
Decimal('1.414213562373095048801688724')
>>> Decimal('NaN')
Decimal('NaN')
>>> Decimal('-Infinity')
Decimal('-Infinity')
```