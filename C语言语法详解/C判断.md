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

### 嵌套if语句

&emsp;&emsp;在`C`语言中，嵌套`if-else`语句是合法的，这意味着你可以在一个`if`或`else if`语句内使用另一个`if`或`else if`语句。

``` cpp
if ( boolean_expression_1 ) {
    /* 当boolean_expression_1为真时执行 */
    if ( boolean_expression_2 ) {
        /* 当boolean_expression_2为真时执行 */
    }
}
```

可以嵌套`else if-else`，方式与嵌套`if`语句相似。

### switch语句

&emsp;&emsp;`switch`语句允许测试一个变量等于多个值时的情况，每个值称为一个`case`：

``` cpp
switch ( expression ) {
    case constant-expression :
        statement(s);
        break; /* 可选的 */
    case constant-expression :
        statement(s);
        break; /* 可选的 */
    /* 可以有任意数量的case语句 */
    default : /* 可选的 */
        statement(s);
}
```

`switch`语句必须遵循下面的规则：

switch语句中的 expression 是一个常量表达式，必须是一个整型或枚举类型。
在一个switch中可以有任意数量的case语句。每个case后跟一个要比较的值和一个冒号。
case的constant-expression必须与switch中的变量具有相同的数据类型，且必须是一个常量或字面量。
当被测试的变量等于case中的常量时，case后跟的语句将被执行，直到遇到break语句为止。
当遇到break语句时，switch终止，控制流将跳转到switch语句后的下一行。
不是每一个case都需要包含break。如果case语句不包含break，控制流将会`继续`后续的case，直到遇到break为止。
一个 switch 语句可以有一个可选的 default case，出现在 switch 的结尾。default case 可用于在上面所有 case 都不为真时执行一个任务。default case 中的 break 语句不是必需的。