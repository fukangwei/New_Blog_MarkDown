---
title: Perl子程序
categories: Perl
date: 2018-12-19 20:06:58
---
&emsp;&emsp;Perl子程序也就是用户定义的函数，可以出现在程序的任何地方：<!--more-->

``` perl
sub subroutine {
    statements;
}
```

调用子程序语法格式：

``` perl
subroutine(参数列表);
```

`Perl 5.0`以下版本调用子程序方法如下：

``` perl
&subroutine(参数列表);
```

示例如下：

``` perl
sub Hello {
    print "Hello, World!\n";
}

Hello();
```

### 向子程序传递参数

&emsp;&emsp;`Perl`子程序可以像其他编程语言一样接受多个参数，子程序参数使用特殊数组`@_`标明，因此子程序第一个参数为`$_[0]`，第二个参数为`$_[1]`，以此类推。不论参数是标量型还是数组型，用户把参数传给子程序时，`perl`默认按引用的方式调用它们。

``` perl
sub Average {
    $n = scalar(@_);
    $sum = 0;

    foreach $item(@_) {
        $sum += $item;
    }

    $average = $sum / $n;
    print '传入的参数为：', "@_\n";
    print "第一个参数值为：$_[0]\n";
    print "传入参数的平均值为：$average\n";
}

Average(10, 20, 30);
```

用户可以通过改变`@_`数组中的值来改变相应实际参数的值。

### 向子程序传递列表

&emsp;&emsp;由于`@_`变量是一个数组，所以它可以向子程序中传递列表。但如果我们需要传入标量和数组参数时，需要把列表放在最后一个参数上：

``` perl
sub PrintList {
    my @list = @_;
    print "列表为：@list\n";  # 输出“列表为：10 1 2 3 4”
}

$a = 10;
@b = (1, 2, 3, 4);

PrintList($a, @b);
```

可以向子程序传入多个数组和哈希，但是在传入这种参数时，会导致丢失独立的标识，所以需要使用引用来传递。

### 向子程序传递哈希

&emsp;&emsp;当向子程序传递哈希表时，它将复制到`@_`中，哈希表将被展开为键/值组合的列表：

``` perl
sub PrintHash {
    my (%hash) = @_;

    foreach my $key ( keys %hash ) {
        my $value = $hash{$key};
        print "$key：$value\n";
    }
}

%hash = ( 'name' => 'runoob', 'age' => 3 );
PrintHash(%hash);
```

执行结果：

``` bash
name：runoob
age：3
```

### 子程序返回值

&emsp;&emsp;子程序可以像其他编程语言一样使用`return`语句来返回函数值。如果没有使用`return`语句，则子程序的最后一行语句将作为返回值。

``` perl
sub add_a_b {
    $_[0] + $_[1];  # 不使用return
    # return $_[0] + $_[1];  # 使用return
}

print add_a_b( 1, 2 )
```

在子程序中，我们可以返回标量、数组和哈希，但是在返回多个数组和哈希时，会导致丢失独立的标识。所以需要使用引用来返回多个数组和函数。

### 子程序的私有变量

&emsp;&emsp;默认情况下，`Perl`中所有的变量都是全局变量，这就是说变量在程序的任何地方都可以调用。如果我们需要设置私有变量，可以使用`my`操作符来设置。
&emsp;&emsp;`my`操作符用于创建词法作用域变量，通过`my`创建的变量，存活于声明开始的地方，直到闭合作用域的结尾。闭合作用域指的可以是一对花括号中的区域，可以是一个文件，也可以是一个`if`、`while`、`for`、`foreach`、`eval`字符串。以下实例演示如何声明一个或多个私有变量：

``` perl
sub somefunc {
    my $variable;  # “$variable”在方法somefunc外不可见
    my ( $another, @an_array, %a_hash );  # 同时声明多个变量
}
```

示例如下：

``` perl
$string = "Hello, World!";  # 全局变量

sub PrintHello{
    my $string;  # PrintHello函数的私有变量
    $string = "Hello, Runoob!";
    print "函数内字符串：$string\n";  # 输出“函数内字符串：Hello, Runoob!”
}

PrintHello();
print "函数外字符串：$string\n";  # 输出“函数外字符串：Hello, World!”
```

### 变量的临时赋值

&emsp;&emsp;可以使用`local`为全局变量提供临时的值，在退出作用域后将原来的值还回去。`local`定义的变量不存在于主程序中，存在于该子程序和该子程序调用的子程序中：

``` perl
$string = "Hello, World!";

sub PrintRunoob {
    local $string;
    $string = "Hello, Runoob!";
    PrintMe();
    print "PrintRunoob函数内字符串值：$string\n";
}

sub PrintMe {
    print "PrintMe函数内字符串值：$string\n";
}

sub PrintHello {
    print "PrintHello函数内字符串值：$string\n";
}

PrintRunoob();
PrintHello();
print "函数外部字符串值：$string\n";
```

执行结果：

``` bash
PrintMe函数内字符串值：Hello, Runoob!
PrintRunoob函数内字符串值：Hello, Runoob!
PrintHello函数内字符串值：Hello, World!
函数外部字符串值：Hello, World!
```

### 静态变量

&emsp;&emsp;`state`操作符功能类似于`C`里面的`static`修饰符，`state`关键字将局部变量变得持久。`state`也是词法变量，所以只在定义该变量的词法作用域中有效：

``` perl
use feature 'state';

sub PrintCount {
    state $count = 0;  # 初始化变量
    print "counter值为：$count\n";
    $count++;
}

for ( 1 .. 3 ) {
    PrintCount();
}
```

执行结果：

``` bash
counter值为：0
counter值为：1
counter值为：2
```

### 子程序调用上下文

&emsp;&emsp;子程序调用过程中，会根据上下文来返回不同类型的值，比如以下`localtime`子程序，在标量上下文返回字符串，在列表上下文返回列表：

``` perl
my $datestring = localtime(time);
print $datestring;  # 输出“Thu Oct 25 22:11:28 2018”
print "\n";
( $sec, $min, $hour, $mday, $mon, $year, $wday, $yday, $isdst ) = localtime(time);
# 输出“2108-10-25 22:11:28”
printf( "%d-%d-%d %d:%d:%d", $year + 1990, $mon + 1, $mday, $hour, $min, $sec );
```