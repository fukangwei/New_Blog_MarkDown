---
title: inf和nan
categories: C语言语法详解
date: 2018-12-10 21:01:10
---
&emsp;&emsp;`inf`：`infinity`(`Linux`平台)等同于`#INF`(`infinity`，`Windows`平台)。<!--more-->
&emsp;&emsp;`nan`：`not a number`(`Linux`平台)等同于`#IND`(`indeterminate`，`Windows`平台)。
&emsp;&emsp;注意以下几点：

- `inf`一般是因为得到的数值超出浮点数的表示范围(溢出，即阶码部分超过其能表示的最大值)；而`nan`一般是因为对浮点数进行了未定义的操作，如对`-1`开方。
- `nan == nan`的结果是`0`或`false`，即不能和`nan`进行比较，和`nan`进行比较得到的结果总是`false`或`0`。所以可以用函数：

``` cpp
int isNumber ( double d ) {
    return ( d == d );
}
```

来判断`d`是否为`nan`。若`d`是`nan`，则返回`0`，否则返回`非0值`。

- `1.0/0.0`等于`inf`，`-1.0/0.0`等于`-inf`，`0.0 + inf`等于`inf`。
- 对负数开方`sqrt(-1.0)`、对负数求对数`log(-1.0)`、`0.0/0.0`、`0.0 * inf`、`inf / inf`、`inf - inf`这些操作都会得到`nan`(注意，`0/0`会产生操作异常；`0.0/0.0`不会产生操作异常，而是会得到`nan`)。
- 得到`inf`时，就查看是否有溢出或者除以`0`；得到`nan`时，就查看是否有非法操作。
- `C`语言头文件`float.h`中，有定义的常量`DBL_MAX`，这个常量表示`能表示出来的最大的双精度浮点型数值`。`float.h`中还有常量`DBL_MIN`，`DBL_MIN`表示可以用规格化表示的最小的正浮点数，但`DBL_MIN`并不是最小的正浮点数，因为可以用可以用非规格化浮点数表示的更小。可以用函数：

``` cpp
int isFiniteNumber ( double d ) {
    return ( d <= DBL_MAX && d >= -DBL_MAX );
}
```

来判断`d`是否为一个`finite`数(既不是`inf`，又不是`nan`)。假如`d`为`nan`，则`d`参加比较就会得到`false`(`0`)值。

- `1.0/inf`等于`0.0`。
- `inf`是可以与其他浮点数进行比较的，即可以参与`<=`、`>=`、`==`、`!=`等运算。

&emsp;&emsp;下面这几个宏(用宏实现的，使用时跟函数的形式基本相同)用于判断一个表达式的结果是否为`inf`、`nan`或其他，其头文件为`math.h`：

``` cpp
int fpclassify(x);
int isfinite(x);
int isnormal(x);
int isnan(x);
int isinf(x);
```

&emsp;&emsp;具体用法为：

- `int fpclassify(x);`用来查看浮点数`x`的情况。它可以用任何浮点数表达式作为参数，其返回值有以下几种情况：

1. `FP_NAN`：`x`是一个`not a number`。
2. `FP_INFINITE`：`x`是正、负无穷。
3. `FP_ZERO`：`x`是`0`。
4. `FP_SUBNORMAL`：`x`太小，以至于不能用浮点数的规格化形式表示。
5. `FP_NORMAL`：`x`是一个正常的浮点数(不是以上结果中的任何一种)。

- `int isfinite(x)`：当`fpclassify ( x ) != FP_NAN && fpclassify ( x ) != FP_INFINITE`时，此宏得到一个`非0值`。
- `int isnormal(x)`：当`fpclassify ( x ) == FP_NORMAL`时，此宏得到一个`非0值`。
- `int isnan(x)`：当`fpclassify ( x ) == FP_NAN`时，此宏返回一个`非0值`。
- `int isinf(x)`：当`x`是正无穷是返回`1`，当`x`是负无穷时返回`-1`。