---
title: Perl条件语句
date: 2018-12-20 15:01:04
tags:
---

&emsp;&emsp;Perl条件语句是通过一条或多条语句的执行结果(True或者False)来决定执行的代码块。注意，数字`0`、字符串`0` 、`""`、空list和undef为false，其他值均为true。true前面使用`!`或not则返回false。

### IF语句

&emsp;&emsp;Perl的if语句由一个布尔表达式后跟一个或多个语句组成。语法格式如下所示：

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

### IF...ELSE语句

&emsp;&emsp;一个if语句后可跟一个可选的else语句，else语句在布尔表达式为false时执行。语法格式如下所示：

``` perl
if ( boolean_expression ) {
    # 在布尔表达式boolean_expression为true时执行
} else{
    # 在布尔表达式boolean_expression为false时执行
}
```

### IF...ELSIF语句

&emsp;&emsp;一个if语句后可跟一个可选的elsif语句，然后再跟另一个else语句。这种条件判断语句在多个条件的情况下非常有用。在使用if、elsif、else语句时，需要注意以下几点：

- if语句后可以跟上0个或1个else语句，但是elsif后面必须有else语句。
- if语句后可以跟上0个或1个elsif语句，但它们必须写在else语句前。
- 如果其中的一个elsif执行成功，其他的elsif和else将不再被执行。

语法格式如下所示：

``` perl
if ( boolean_expression_1 ) {
    # 在布尔表达式boolean_expression_1为true时执行
}
elsif ( boolean_expression_2 ) {
    # 在布尔表达式boolean_expression_2为true时执行
}
elsif ( boolean_expression_3 ) {
    # 在布尔表达式boolean_expression_3为true时执行
}
else {
    # 布尔表达式的条件都为false时执行
}
```

### UNLESS语句

&emsp;&emsp;一个unless语句由一个布尔表达式后跟一个或多个语句组成。语法格式如下所示：

``` perl
unless ( boolean_expression ) {
    # 在布尔表达式boolean_expression为false时执行
}
```

如果布尔表达式boolean_expression为false，则if语句内的代码块将被执行，否则不执行。

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

### UNLESS...ELSE语句

&emsp;&emsp;一个unless语句后可跟一个可选的else语句，else语句在布尔表达式为true时执行。语法格式如下所示：

``` perl
unless ( boolean_expression ) {
    # 在布尔表达式boolean_expression为false时执行
} else {
    # 在布尔表达式boolean_expression为时true执行
}
```

### UNLESS...ELSIF语句

&emsp;&emsp;一个unless语句后可跟一个可选的elsif语句，然后再跟另一个else语句。这种条件判断语句在多个条件的情况下非常有用。在使用unless、elsif和else语句时，需要注意以下几点：

- unless语句后可以跟上0个或1个else语句，但是elsif后面必须有else语句。
- unless语句后可以跟上0个或1个elsif语句，但它们必须写在else语句前。
- 如果其中的一个elsif执行成功，其他的elsif和else将不再被执行。

语法格式如下所示：

``` perl
unless ( boolean_expression_1 ) {
    # 在布尔表达式boolean_expression_1为false时执行
}
elsif ( boolean_expression_2 ) {
    # 在布尔表达式boolean_expression_2为true时执行
}
elsif ( boolean_expression_3 ) {
    # 在布尔表达式boolean_expression_3为true时执行
}
else {
    # 没有条件匹配时执行
}
```

示例如下：

``` perl
$a = 20;
​
unless ( $a  ==  30 ) {
    printf "a的值不为30\n"; # 输出“a的值不为30”
} elsif ( $a ==  30 ) {
    printf "a的值为30\n";
} else {
    printf "a的值为$a\n";
}
```

### switch语句

&emsp;&emsp;一个switch语句允许测试一个变量等于多个值时的情况。每个值称为一个case，且被测试的变量会对每个`switch case`进行检查。`switch case`的执行是基于Switch模块，Switch模块默认是没有安装的。
&emsp;&emsp;使用CPAN安装`Switch.pm`模块：打开终端，输入cpan命令，然后输入如下命令：

``` bash
cpan[1]> install Switch // 安装
cpan[2]> exit // 退出
```

语法格式如下所示：

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

以下是switch语句的规则:

- switch语句的括号中可以使用任意类型的标量参数。
- 在一个switch中可以有任意数量的case语句，每个case后跟一个要比较的值和一个冒号。
- case语句后的标量会与switch语句的标量进行比较，判断是否相等。
- 当被测试的变量等于case中的常量时，case后跟的语句将被执行，直到遇到break语句为止。
- switch语句可以有一个可选的else，该语句在最后面，该语句在所有case不匹配的情况下执行。
- 当匹配case后，会执行case语句块代码，执行后跳出switch语句。
- 当匹配case后，如果我们需要继续执行接下来的case语句，则需要添加next语句。

示例如下：

``` perl
use Switch;
​
$var = 10;
@array = (10, 20, 30);
%hash = ('key1' => 10, 'key2' => 20);
​
switch ( $var ) {
    case 10         {print "数字10\n"} # 输出“数字10”
    case "a"        {print "字符串a"}
    case [1..10,42] {print "数字在列表中"}
    case (\@array)  {print "数字在数组中"}
    case (\%hash)   {print "在哈希中"}
    else            {print "没有匹配的条件"}
}
```

使用next的示例：

``` perl
use Switch;
​
$var = 10;
@array = (10, 20, 30);
%hash = ('key1' => 10, 'key2' => 20);
​
switch ( $var ) {
    case 10         {print "数字10\n"; next;} # 匹配后继续执行
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