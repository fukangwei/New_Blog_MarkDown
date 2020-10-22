---
title: CSharp判断
date: 2020-03-21 02:50:41
categories: C#
---
### if语句

&emsp;&emsp;语法如下：<!--more-->

``` cs
if (boolean_expression) {
    /* 如果布尔表达式为真，将执行的语句 */
}
```

如果布尔表达式为`true`，则`if`语句内的代码块将被执行，否则不执行。

### if-else语句

&emsp;&emsp;语法如下：

``` cs
if (boolean_expression) {
    /* 如果布尔表达式为真，将执行的语句 */
} else {
    /* 如果布尔表达式为假，将执行的语句 */
}
```

如果布尔表达式为`true`，则执行`if`块内的代码；如果布尔表达式为`false`，则执行`else`块内的代码。

### if-else if-else语句

&emsp;&emsp;当使用该语句时，以下几点需要注意：

1. 一个`if`后可跟零个或一个`else`，它必须在任何一个`else if`之后。
2. 一个`if`后可跟零个或多个`else if`，它们必须在`else`之前。
3. 一旦某个`else if`匹配成功，其他的`else if`或`else`将不会被测试。

``` cs
if (boolean_expression_1) {
    /* 当boolean_expression_1为真时执行 */
} else if (boolean_expression_2) {
    /* 当boolean_expression_2为真时执行 */
} else if (boolean_expression_3) {
    /* 当boolean_expression_3为真时执行 */
} else {
    /* 当上面条件都不为真时执行 */
}
```

### 嵌套if语句

&emsp;&emsp;在`C#`中，嵌套`if-else`语句是合法的：

``` cs
if(boolean_expression_1) {
    /* 当boolean_expression_1为真时执行 */
    if(boolean_expression_2) {
        /* 当boolean_expression_2为真时执行 */
    }
}
```

### switch语句

&emsp;&emsp;一个`switch`语句允许测试一个变量等于多个值时的情况。每个值称为一个`case`，且被测试的变量会对每个`switch case`进行检查。

``` cs
switch (expression) {
    case constant-expression :
        statements;
        break;
    case constant-expression :
        statements;
        break;
    /* 你可以有任意数量的case语句 */
    default : /* 可选的 */
        statements;
        break;
}
```

&emsp;&emsp;`switch`语句必须遵循下面的规则：

- `switch`语句中的`expression`必须是一个整型或枚举类型，或者是一个`class`类型，其中`class`有一个单一的转换函数将其转换为整型或枚举类型。
- 在一个`switch`中可以有任意数量的`case`语句。每个`case`后跟一个要比较的值和一个冒号。
- `case`的`constant-expression`必须与`switch`中的变量具有相同的数据类型，且必须是一个常量。
- 当被测试的变量等于`case`中的常量时，`case`后跟的语句将被执行，直到遇到`break`语句为止。
- 当遇到`break`语句时，`switch`终止，控制流将跳转到`switch`语句后的下一行。
- 不是每一个`case`都需要包含`break`。如果`case`语句为空，则可以不包含`break`，控制流将会继续后续的`case`，直到遇到`break`为止。
- 一个`switch`语句可以有一个可选的`default case`，出现在`switch`的结尾。`default case`可用于在上面所有`case`都不为真时执行一个任务。`default case`中的`break`语句不是必需的。

### 嵌套switch语句

&emsp;&emsp;语法如下：

``` cs
switch (ch1) {
    case 'A':
        printf("这个 A 是外部 switch 的一部分" );

        switch (ch2) {
            case 'A':
                printf("这个 A 是内部 switch 的一部分" );
                break;
            case 'B': /* 内部 B case 代码 */
        }

        break;
    case 'B': /* 外部 B case 代码 */
}
```

### ?:运算符

&emsp;&emsp;语法如下，其中`Exp1`、`Exp2`和`Exp3`是表达式：

``` cs
Exp1 ? Exp2 : Exp3;
```

`?`表达式的值是由`Exp1`决定的：

1. 如果`Exp1`为真，则计算`Exp2`的值，结果即为整个`?`表达式的值。
2. 如果`Exp1`为假，则计算`Exp3`的值，结果即为整个`?`表达式的值。