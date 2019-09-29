---
title: math模块
categories: Python语法
abbrlink: 6cf85dea
date: 2018-12-27 19:35:46
---
### 数学常量

&emsp;&emsp;常数如下：

- `pi`：圆周率`π`。
- `e`：数学常数`e`。

### 数学函数

&emsp;&emsp;`ceil(number)`：返回一个大于或等于`number`的的最小整数，例如`ceil(4.1)`返回`5`。
&emsp;&emsp;`cmp(x, y)`：如果`x < y`，则返回`-1`；如果`x == y`，则返回`0`；如果`x > y`，则返回`1`。`Python 3`已废弃。
&emsp;&emsp;`exp(x)`：返回`e`的`x`次幂，如`exp(1)`返回`2.718281828459045`。
&emsp;&emsp;`fabs(x)`：返回数字的绝对值，例如`math.fabs(-10)`返回`10.0`。`abs`是内置函数，`fabs`函数在`math`模块中定义；`fabs`函数只对浮点型跟整型数值有效，`abs`还可以运用在复数中。
&emsp;&emsp;`floor(number)`：返回数的下舍整数，如`floor(4.9)`返回`4`。
&emsp;&emsp;`log(x)`：`math.log(math.e)`返回`1.0`，`math.log(100, 10)`返回`2.0`。
&emsp;&emsp;`log10(x)`：返回以`10`为基数的`x`的对数，例如`math.log10(100)`返回`2.0`。
&emsp;&emsp;`modf(x)`：返回`x`的整数部分与小数部分，两部分的数值符号与`x`相同，整数部分以浮点型表示。

``` python
import math
​
print("math.modf(100.12):", math.modf(100.12))
print("math.modf(119):", math.modf(119))
print("math.modf(math.pi):", math.modf(math.pi))
```

执行结果：

``` bash
math.modf(100.12): (0.12000000000000455, 100.0)
math.modf(119): (0.0, 119.0)
math.modf(math.pi): (0.14159265358979312, 3.0)
```

&emsp;&emsp;`pow(x, y[, z])`：返回`x`的`y`次幂(所得结果对`z`取模，等效于`pow(x, y) % z`)，例如`pow(2, 3)`返回`8`，`pow(2, 3, 3)`返回`2`。
&emsp;&emsp;`round(x)`：返回浮点数`x`的四舍五入值，函数原型为`round(x[, n=0])`，其中`n`指定小数的位数，即在第`n`位小数上四舍五入。

``` python
print("round(70.23456):", round(70.23456))
print("round(56.659, 1):", round(56.659, 1))
print("round(80.264, 2):", round(80.264, 2))
print("round(-100.000056, 3):", round(-100.000056, 3))
```

执行结果：

``` bash
round(70.23456): 70
round(56.659, 1): 56.7
round(80.264, 2): 80.26
round(-100.000056, 3): -100.0
```

&emsp;&emsp;`sqrt(number)`：返回数字的平方根，返回类型为实数。
&emsp;&emsp;`acos(x)`：返回`x`的反余弦弧度值。
&emsp;&emsp;`asin(x)`：返回`x`的反正弦弧度值。
&emsp;&emsp;`atan(x)`：返回`x`的反正切弧度值。
&emsp;&emsp;`atan2(y, x)`：返回给定的`x`和`y`坐标值的反正切值。
&emsp;&emsp;`cos(x)`：返回余弦值，如`cos(pi)`返回`-1.0`。
&emsp;&emsp;`hypot(x, y)`：返回欧几里德范数，即`sqrt(x * x + y * y)`。
&emsp;&emsp;`sin(x)`：返回正弦值，如`sin(pi/2)`返回`1.0`。
&emsp;&emsp;`tan(x)`：返回正切值，如`degrees(tan(1.0))`返回`30.0`。
&emsp;&emsp;`degrees(x)`：将弧度转换为角度，如`degrees(tan(1.0))`返回`30.0`。
&emsp;&emsp;`radians(x)`：将角度转换为弧度。