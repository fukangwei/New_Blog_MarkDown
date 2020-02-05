---
title: Perl条件语句
categories: Perl
abbrlink: 94aee6f4
date: 2018-12-20 15:01:04
---
&emsp;&emsp;`Perl`条件语句是通过一条或多条语句的执行结果(`True`或者`False`)来决定执行的代码块。注意，数字`0`、字符串`0`、`""`、`空list`和`undef`为`false`，其他值均为`true`。`true`前面使用`!`或`not`则返回`false`。<!--more-->

### if语句

&emsp;&emsp;`Perl`的`if`语句由一个布尔表达式后跟一个或多个语句组成：

``` perl
if ( boolean_expression ) {
    # 在布尔表达式boolean_expression为true时执行
}
```

示例如下：

``` perl
$a = 10;
​
if ( $a < 20 ){
    printf "a小于20\n";
}
​
print "a的值为：$a\n";

$a = "";
​
if ( $a ) {
    printf "变量a为true\n";
}

print "a的值为：$a\n";
```

执行结果：

``` bash
a小于20
a的值为：10
a的值为：
```

### if-else语句

&emsp;&emsp;一个`if`语句后可跟一个可选的`else`语句，`else`语句在布尔表达式为`false`时执行：

``` perl
if ( boolean_expression ) {
    # 在布尔表达式boolean_expression为true时执行
} else{
    # 在布尔表达式boolean_expression为false时执行
}
```

### if-elsif语句

&emsp;&emsp;一个`if`语句后可跟一个可选的`elsif`语句，然后再跟另一个`else`语句。这种条件判断语句在多个条件的情况下非常有用。在使用`if`、`elsif`、`else`语句时，需要注意以下几点：

- `if`语句后可以跟上`0`个或`1`个`else`语句，但是`elsif`后面必须有`else`语句。
- `if`语句后可以跟上`0`个或`1`个`elsif`语句，但它们必须写在`else`语句前。
- 如果其中的一个`elsif`执行成功，其他的`elsif`和`else`将不再被执行。

``` perl
if ( boolean_expression_1 ) {
    # 在布尔表达式boolean_expression_1为true时执行
} elsif ( boolean_expression_2 ) {
    # 在布尔表达式boolean_expression_2为true时执行
} elsif ( boolean_expression_3 ) {
    # 在布尔表达式boolean_expression_3为true时执行
} else {
    # 布尔表达式的条件都为false时执行
}
```

### unless语句

&emsp;&emsp;一个`unless`语句由一个布尔表达式后跟一个或多个语句组成：

``` perl
unless ( boolean_expression ) {
    # 在布尔表达式boolean_expression为false时执行
}
```

如果布尔表达式`boolean_expression`为`false`，则`if`语句内的代码块将被执行，否则不执行。

``` perl
$a = 20;
​
unless ( $a < 20 ) {
    printf "a大于等于20\n";
}

print "a的值为：$a\n";

$a = "";
​
unless ( $a ) {
    printf "条件a为false\n";
}

print "a的值为：$a\n";
```

执行结果：

``` bash
a大于等于20
a的值为：20
条件a为false
a的值为：
```

### unless-else语句

&emsp;&emsp;一个`unless`语句后可跟一个可选的`else`语句，`else`语句在布尔表达式为`true`时执行：

``` perl
unless ( boolean_expression ) {
    # 在布尔表达式boolean_expression为false时执行
} else {
    # 在布尔表达式boolean_expression为时true执行
}
```

### unless-elsif语句

&emsp;&emsp;一个`unless`语句后可跟一个可选的`elsif`语句，然后再跟另一个`else`语句。这种条件判断语句在多个条件的情况下非常有用。在使用`unless`、`elsif`和`else`语句时，需要注意以下几点：

- `unless`语句后可以跟上`0`个或`1`个`else`语句，但是`elsif`后面必须有`else`语句。
- `unless`语句后可以跟上`0`个或`1`个`elsif`语句，但它们必须写在`else`语句前。
- 如果其中的一个`elsif`执行成功，其他的`elsif`和`else`将不再被执行。

``` perl
unless ( boolean_expression_1 ) {
    # 在布尔表达式boolean_expression_1为false时执行
} elsif ( boolean_expression_2 ) {
    # 在布尔表达式boolean_expression_2为true时执行
} elsif ( boolean_expression_3 ) {
    # 在布尔表达式boolean_expression_3为true时执行
} else {
    # 没有条件匹配时执行
}
```

示例如下：

``` perl
$a = 20;
​
unless ( $a  ==  30 ) {
    printf "a的值不为30\n";  # 输出“a的值不为30”
} elsif ( $a ==  30 ) {
    printf "a的值为30\n";
} else {
    printf "a的值为$a\n";
}
```

### switch语句

&emsp;&emsp;一个`switch`语句允许测试一个变量等于多个值时的情况。每个值称为一个`case`，且被测试的变量会对每个`switch case`进行检查。`switch case`的执行是基于`Switch`模块，`Switch`模块默认是没有安装的。
&emsp;&emsp;使用`CPAN`安装`Switch.pm`模块：打开终端，输入`cpan`命令，然后输入如下命令：

``` bash
cpan[1]> install Switch // 安装
cpan[2]> exit // 退出
```

语法格式如下：

``` perl
use Switch;

switch(argument){
    case 1          {print "数字1"}
    case "a"        {print "字符串a"}
    case [1..10,42] {print "数字在列表中"}
    case (\@array)  {print "数字在数组中"}
    case /\w+/      {print "正则匹配模式"}
    case qr/\w+/    {print "正则匹配模式"}
    case (\%hash)   {print "哈希"}
    case (\&sub)    {print "子进程"}
    else            {print "不匹配之前的条件"}
}
```

以下是`switch`语句的规则:

- `switch`语句的括号中可以使用任意类型的标量参数。
- 在一个`switch`中可以有任意数量的`case`语句，每个`case`后跟一个要比较的值和一个冒号。
- `case`语句后的标量会与`switch`语句的标量进行比较，判断是否相等。
- 当被测试的变量等于`case`中的常量时，`case`后跟的语句将被执行，直到遇到`break`语句为止。
- `switch`语句可以有一个可选的`else`，该语句在最后面，该语句在所有`case`不匹配的情况下执行。
- 当匹配`case`后，会执行`case`语句块代码，执行后跳出`switch`语句。
- 当匹配`case`后，如果我们需要继续执行接下来的`case`语句，则需要添加`next`语句。

``` perl
use Switch;
​
$var = 10;
@array = (10, 20, 30);
%hash = ('key1' => 10, 'key2' => 20);
​
switch ( $var ) {
    case 10         {print "数字10\n"}  # 输出“数字10”
    case "a"        {print "字符串a"}
    case [1..10,42] {print "数字在列表中"}
    case (\@array)  {print "数字在数组中"}
    case (\%hash)   {print "在哈希中"}
    else            {print "没有匹配的条件"}
}
```

使用`next`的示例：

``` perl
use Switch;
​
$var = 10;
@array = (10, 20, 30);
%hash = ('key1' => 10, 'key2' => 20);
​
switch ( $var ) {
    case 10         {print "数字10\n"; next;}  # 匹配后继续执行
    case "a"        {print "string a"}
    case [1..10,42] {print "数字在列表中"}
    case (\@array)  {print "数字在数组中"}
    case (\%hash)   {print "在哈希中"}
    else            {print "没有匹配的条件"}
}
```

执行结果：

``` bash
数字10
数字在列表中
```