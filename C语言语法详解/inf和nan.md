---
title: inf和nan
date: 2018-12-10 21:01:10
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C/C++中的inf与nan](https://blog.csdn.net/u012707739/article/details/79143320)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;`inf`：`infinity`(Linux平台)等同于`#INF`(infinity，Windows平台)。
&emsp;&emsp;`nan`：`not a number`(Linux平台)等同于`#IND`(indeterminate，Windows平台)。
&emsp;&emsp;注意以下几点：

- inf一般是因为得到的数值超出浮点数的表示范围(溢出，即阶码部分超过其能表示的最大值)；而nan一般是因为对浮点数进行了未定义的操作，如对`-1`开方。
- `nan == nan`的结果是0或false，即不能和nan进行比较，和nan进行比较得到的结果总是false或0。所以可以用函数：

``` c
int isNumber ( double d ) {
    return ( d == d );
}
```

来判断d是否为nan。若d是nan则返回0，否则返回非零值。

- `1.0/0.0`等于`inf`，`-1.0/0.0`等于`-inf`，`0.0 + inf`等于inf。
- 对负数开方`sqrt(-1.0)`、对负数求对数`log(-1.0)`、`0.0/0.0`、`0.0 * inf`、`inf / inf`、`inf - inf`这些操作都会得到nan(注意，`0/0`会产生操作异常；`0.0/0.0`不会产生操作异常，而是会得到nan)。
- 得到inf时，就查看是否有溢出或者除以0；得到nan时，就查看是否有非法操作。

- C语言的头文件`float.h`中，有定义的常量`DBL_MAX`，这个常量表示`能表示出来的最大的双精度浮点型数值`。`float.h`中还有常量`DBL_MIN`，`DBL_MIN`表示可以用规格化表示的最小的正浮点数，但`DBL_MIN`并不是最小的正浮点数，因为可以用可以用非规格化浮点数表示的更小。可以用函数：

``` c
int isFiniteNumber ( double d ) {
    return ( d <= DBL_MAX && d >= -DBL_MAX );
}
```

来判断d是否为一个finite数(既不是inf，又不是nan)。假如d为nan，则d参加比较就会得到false(0)值。

- `1.0/inf`等于0.0。
- inf是可以与其他浮点数进行比较的，即可以参与`<=`、`>=`、`==`、`!=`等运算。

&emsp;&emsp;下面这几个宏(用宏实现的，使用时跟函数的形式基本相同)用于判断一个表达式的结果是否为`inf`、`nan`或其他，其头文件为`math.h`：

``` c
int fpclassify(x);
int isfinite(x);
int isnormal(x);
int isnan(x);
int isinf(x);
```

&emsp;&emsp;具体用法为：

- `int fpclassify(x);`用来查看浮点数x的情况。它可以用任何浮点数表达式作为参数，其返回值有以下几种情况：
1. `FP_NAN`：x是一个`not a number`。
2. `FP_INFINITE`：x是正、负无穷。
3. `FP_ZERO`：x是0。
4. `FP_SUBNORMAL`：x太小，以至于不能用浮点数的规格化形式表示。
5. `FP_NORMAL`：x是一个正常的浮点数(不是以上结果中的任何一种)。
- `int isfinite(x)`：当`fpclassify ( x ) != FP_NAN && fpclassify ( x ) != FP_INFINITE`时，此宏得到一个非零值。
- `int isnormal(x)`：当`fpclassify ( x ) == FP_NORMAL`时，此宏得到一个非零值。
- `int isnan(x)`：当`fpclassify ( x ) == FP_NAN`时，此宏返回一个非零值。
- `int isinf(x)`：当x是正无穷是返回1，当x是负无穷时返回`-1`(注意，有些较早的编译器版本中，无论是正无穷还是负无穷，都返回非零值，不区分正负无穷)。