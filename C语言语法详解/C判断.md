---
title: C判断
date: 2019-10-02 07:29:47
categories: C语言语法详解
---
&emsp;&emsp;判断结构要求程序员指定`一个`或`多个`要评估或测试的条件，以及条件为真时要执行的语句和条件为假时要执行的语句。
&emsp;&emsp;`C`语言把任何`非零`和`非空`的值设定为`true`，把`0`或`null`设定为`false`。

### if语句

&emsp;&emsp;`if`语句由一个布尔表达式后跟`一个`或`多个`语句组成：

``` cpp
if ( boolean_expression ) {
    /* 如果布尔表达式为真，则将要执行的语句 */
}
```

### if-else语句

&emsp;&emsp;`if`语句后可跟一个可选的`else`语句：

``` cpp
if ( boolean_expression ) {
    /* 如果布尔表达式为真，则将执行的语句 */
} else {
    /* 如果布尔表达式为假，则将执行的语句 */
}
```

### if-else if-else语句

&emsp;&emsp;`if`语句后可跟一个可选的`else if-else`语句，用于测试多种条件。当使用`if-else if-else`语句时，以下几点需要注意：

- `if`后可跟`0`个或`1`个`else`，`else`必须在所有`else if`之后。
- `if`后可跟`0`个或`N`个`else if`，`else if`必须在`else`之前。
- 一旦某个`else if`匹配成功，其他的`else if`或`else`将不会被测试。

``` cpp
if ( boolean_expression_1 ) {
    /* 当boolean_expression_1为真时执行 */
} else if ( boolean_expression_2 ) {
    /* 当boolean_expression_2为真时执行 */
} else if( boolean_expression_3 ) {
    /* 当boolean_expression_3为真时执行 */
} else {
    /* 当上面条件都不为真时执行 */
}
```