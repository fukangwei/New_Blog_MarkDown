---
title: C判断
categories: C语言语法详解
date: 2019-10-02 07:29:47
---
&emsp;&emsp;判断结构要求程序员指定`一个`或`多个`要评估或测试的条件，以及条件为真时要执行的语句、条件为假时要执行的语句。<!--more-->
&emsp;&emsp;`C`语言把任何`非零`和`非空`的值设定为`true`，把`0`或`null`设定为`false`。

### if语句

&emsp;&emsp;`if`语句由一个布尔表达式后跟`一个`或`多个`语句组成：

``` cpp
if ( boolean_expression ) {
    /* 如果boolean_expression为真，则将要执行的语句 */
}
```

### if-else语句

&emsp;&emsp;`if`语句后可跟一个可选的`else`语句：

``` cpp
if ( boolean_expression ) {
    /* 如果boolean_expression为真，则将执行的语句 */
} else {
    /* 如果boolean_expression为假，则将执行的语句 */
}
```

### if-else if-else语句

&emsp;&emsp;`if`语句后可跟一个可选的`else if-else`语句，用于测试多种条件：

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

&emsp;&emsp;在`C`语言中，嵌套`if-else`语句是合法的，这意味着你可以在一个`if`或`else if`语句内，使用另一个`if`或`else if`语句：

``` cpp
if ( boolean_expression_1 ) {
    /* 当boolean_expression_1为真时执行 */
    if ( boolean_expression_2 ) {
        /* 当boolean_expression_2为真时执行 */
    }
}
```

### switch语句

&emsp;&emsp;`switch`语句用于测试一个变量等于多个值时的情况，每个值称为一个`case`：

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

&emsp;&emsp;`switch`语句必须遵循下面的规则：

- `expression`是一个常量表达式，必须是一个`整型`或`枚举类型`。
- 当`expression`等于`case`中的常量时，`case`后跟的语句将被执行，直到遇到`break`语句为止。
- 当遇到`break`语句时，`switch`终止，控制流将跳转到`switch`语句后的下一行。
- 不是每一个`case`都需要包含`break`。如果`case`语句不包含`break`，控制流将会`继续`后续的`case`，直到遇到`break`为止。
- `switch`语句有一个可选的`default case`，出现在`switch`的结尾，用于在上面所有`case`都不为真时执行一个任务。

### 嵌套switch语句

&emsp;&emsp;可以在一个`switch`语句内使用另一个`switch`语句，即使内部和外部`switch`的`case`常量包含共同的值，也没有矛盾：

``` cpp
switch(ch1) {
    case 'A':
        printf("这个A是外部switch的一部分" );

        switch(ch2) {
            case 'A':
                printf("这个A是内部switch的一部分" );
                break;
            case 'B': /* 内部B case代码 */
        }

        break;
    case 'B': /* 外部B case代码 */
}
```

### 三元运算符

&emsp;&emsp;三元运算符是`?:`，例如`value = Con ? X : Y`表示如果`Con`为真，则`value`的值为`X`，否则为`Y`。

``` cpp
#include <stdio.h>

int main() {
    int a, b;
    a = 10;
    b = ( a == 1 ) ? 20 : 30;
    printf ( "b的值是%d\n", b ); /* b的值是30 */
    b = ( a == 10 ) ? 20 : 30;
    printf ( "b的值是%d\n", b ); /* b的值是20 */
}
```