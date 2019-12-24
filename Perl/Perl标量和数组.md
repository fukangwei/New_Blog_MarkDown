---
title: Perl标量和数组
categories: Perl
abbrlink: 5f4f3693
date: 2018-12-20 15:33:48
---
### 创建变量

&emsp;&emsp;变量不需要显式声明类型，在变量赋值后，解释器会自动分配匹配的类型空间。我们可以在程序中使用`use strict;`语句让所有变量需要强制声明类型。

``` perl
$age = 25;  # 整型
$name = "runoob";  # 字符串
$salary = 1445.50;  # 浮点数
```

### 变量上下文

&emsp;&emsp;所谓上下文指的是表达式所在的位置。上下文是由等号左边的变量类型决定的，等号左边是标量，则是标量上下文；等号左边是列表，则是列表上下文。`Perl`解释器会根据上下文来决定变量的类型。

``` perl
@names = ('google', 'runoob', 'taobao');

@copy = @names;  # 复制数组
$size = @names;  # 数组赋值给标量，返回数组元素个数

print "名字为：@copy\n";  # 输出“名字为：google runoob taobao”
print "名字数为：$size\n";  # 输出“名字数为：3”
```

`@names`是一个数组，它应用在了两个不同的上下文中。第一个将其复制给另外一个数组，所以它输出了数组的所有元素；第二个将其赋值给一个标量，它返回了数组的元素个数。以下列出了多种不同的上下文：

- `标量`：赋值给一个标量变量，在标量上下文的右侧计算
- `列表`：赋值给一个数组或哈希，在列表上下文的右侧计算。
- `布尔`：布尔上下文是一个简单的表达式计算，查看是否为`true`或`false`。
- `Void`：这种上下文不需要关系返回什么值，一般不需要返回值。
- `插值`：这种上下文只发生在引号内。

### 特殊字符

&emsp;&emsp;以下将演示`Perl`中特殊字符的应用，例如`__FILE__`、`__LINE__`和`__PACKAGE__`。它们分别表示当前执行脚本的文件名、行号和包名。这些特殊字符是单独的标记，不能写在字符串中：

``` perl
print "文件名 ".__FILE__."\n";            # 输出“文件名 .\haha.pl”
print "行号 ".__LINE__."\n";              # 输出“行号 2”
print "包名 ".__PACKAGE__."\n";           # 输出“包名 main”
print "__FILE__ __LINE__ __PACKAGE__\n";  # 无法解析
```

### 标量变量

&emsp;&emsp;标量是一个单一的数据单元，数据可以是整数、浮点数、字符、字符串等，简单的说它可以是任何东西。以下是标量的简单应用：

``` perl
$age = 25;  # 整型
$name = "runoob";  # 字符串
$salary = 1445.50;  # 浮点数
​
print "Age = $age\n";  # 输出“Age = 25”
print "Name = $name\n";  # 输出“Name = runoob”
print "Salary = $salary\n";  # 输出“Salary = 1445.5”
```

&emsp;&emsp;标量的简单运算：

``` perl
$str = "hello" . "world";  # 字符串连接
$num = 5 + 10;  # 两数相加
$mix = $str . $num;  # 连接字符串和数字
​
print "str = $str\n";  # 输出“str = helloworld”
print "num = $num\n";  # 输出“num = 15”
print "mix = $mix\n";  # 输出“mix = helloworld15”
```

### 数组变量

&emsp;&emsp;数组是用于存储一个有序的标量值的变量。要访问数组的变量，可以使用美元符号`$`加上变量名，并指定下标来访问：

``` perl
@ages  = ( 25, 30, 40 );
@names = ( "google", "runoob", "taobao" );
​
print "\$ages[0] = $ages[0]\n";  # 输出“$ages[0] = 25”
print "\$ages[1] = $ages[1]\n";  # 输出“$ages[1] = 30”
print "\$ages[2] = $ages[2]\n";  # 输出“$ages[2] = 40”
print "\$names[0] = $names[0]\n";  # 输出“$names[0] = google”
print "\$names[1] = $names[1]\n";  # 输出“$names[1] = runoob”
print "\$names[2] = $names[2]\n";  # 输出“$names[2] = taobao”
```

也可以使用`qw`定义数组：

``` perl
@array = qw/这是 一个 数组/;
```

该数组返回字符串列表，数组元素以空格分隔。当然也可以使用多行来定义数组：

``` perl
@days = qw/google
taobao
...
runoob/;
```

可以按索引来给数组赋值：

``` perl
$array[0] = 'Monday';
...
$array[6] = 'Sunday';
```

访问数组元素使用格式`$ + 变量名称 + [索引值]`：

``` perl
@sites = qw/google taobao runoob/;
​
print "$sites[0]\n";  # 输出“google”
print "$sites[1]\n";  # 输出“taobao”
print "$sites[-1]\n";  # 负数用于反向读取。输出“runoob”
```

### 数组序列号

&emsp;&emsp;`Perl`提供了可以按序列输出的数组形式，格式为`起始值 + .. + 结束值`：

``` perl
@var_10 = (1..10);
@var_20 = (10..20);
@var_abc = (a..z);
​
print "@var_10\n";  # 输出1到10
print "@var_20\n";  # 输出10到20
print "@var_abc\n";  # 输出a到z
```

### 数组大小

&emsp;&emsp;数组长度返回的是数组物理大小，而不是元素的个数：

``` perl
@array = ( 1, 2, 3 );
$array[50] = 4;
​
$size = @array;
$max_index = $#array;
​
print "数组大小：$size\n";  # 输出“数组大小：51”
print "最大索引：$max_index\n";  # 输出“最大索引：50”
```

### 添加和删除数组元素

&emsp;&emsp;`Perl`提供了一些有用的函数来添加和删除数组元素：

函数                   | 作用
-----------------------|-----
`push @ARRAY, LIST`    | 将列表的值放到数组的末尾
`pop @ARRAY`           | 弹出数组最后一个值，并返回它
`shift @ARRAY`         | 弹出数组第一个值，并返回它。数组的索引值也依次减一
`unshift @ARRAY, LIST` | 将列表放在数组前面，并返回新数组的元素个数

示例如下：

``` perl
@sites = ("google","runoob","taobao");
print "1. \@sites = @sites\n";  # 输出“1. @sites = google runoob taobao”
push(@sites, "baidu");  # 在数组结尾添加一个元素
print "2. \@sites = @sites\n";  # 输出“2. @sites = google runoob taobao baidu”
unshift(@sites, "weibo");  # 在数组开头添加一个元素
print "3. \@sites = @sites\n";  # 输出“3. @sites = weibo google runoob taobao baidu”
pop(@sites);  # 删除数组末尾的元素
print "4. \@sites = @sites\n";  # 输出“4. @sites = weibo google runoob taobao”
shift(@sites);  # 移除数组开头的元素
print "5. \@sites = @sites\n";  # 输出“5. @sites = google runoob taobao”
```

### 切割数组

&emsp;&emsp;我们可以切割一个数组，并返回切割后的新数组：

``` perl
@sites = qw/google taobao runoob weibo qq facebook 网易/;
@sites2 = @sites[ 3, 4, 5 ];
print "@sites2\n";  # 输出“weibo qq facebook”
```

数组索引需要指定有效的索引值，可以是正数或负数，每个索引值使用逗号隔开。如果是连续的索引，可以使用`..`来表示指定范围：

``` perl
@sites = qw/google taobao runoob weibo qq facebook 网易/;
@sites2 = @sites[3..5];
print "@sites2\n";  # 输出“weibo qq facebook”
```

### 替换数组元素

&emsp;&emsp;`Perl`中数组元素替换使用`splice`函数：

``` perl
splice @ARRAY, OFFSET [, LENGTH [, LIST]]
```

`ARRAY`是要替换的数组，`OFFSET`是起始位置，`LENGTH`是替换的元素个数，`LIST`是替换元素列表。以下实例从第`6`个元素开始替换数组中的`5`个元素：

``` perl
@nums = (1..20);
print "替换前 - @nums\n";
splice(@nums, 5, 5, 21..25);
print "替换后 - @nums\n";
```

执行结果：

``` bash
替换前 - 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
替换后 - 1 2 3 4 5 21 22 23 24 25 11 12 13 14 15 16 17 18 19 20
```

### 将字符串转换为数组

&emsp;&emsp;`Perl`中将字符串转换为数组使用`split`函数：

``` perl
split [PATTERN [, EXPR [, LIMIT]]]
```

`PATTERN`是分隔符，默认为空格；`EXPR`指定字符串。

``` perl
$var_test = "runoob";
$var_string = "www-runoob-com";
$var_names = "google,taobao,runoob,weibo";
​
# 字符串转为数组
@test = split('', $var_test);
@string = split('-', $var_string);
@names  = split(',', $var_names);
​
print "$test[3]\n";  # 输出“o”
print "$string[2]\n";  # 输出“com”
print "$names[3]\n";  # 输出“weibo”
```

### 将数组转换为字符串

&emsp;&emsp;`Perl`中将数组转换为字符串使用`join`函数：

``` perl
join EXPR, LIST
```

`EXPR`是连接符，`LIST`是列表或数组。

### 数组排序

&emsp;&emsp;`Perl`中数组排序使用`sort`函数：

``` perl
sort [SUBROUTINE] LIST
```

`SUBROUTINE`指定规则，`LIST`是列表或数组。

``` perl
@sites = qw(google taobao runoob facebook);
print "排序前：@sites\n";  # 输出“排序前：google taobao runoob facebook”

# 对数组进行排序
@sites = sort(@sites);
print "排序后：@sites\n";  # 输出“排序后：facebook google runoob taobao”
```

注意，数组排序是根据`ASCII`数字值来排序，所以我们在对数组进行排序时，最好先将每个元素转换为小写后再排序。

### 合并数组

&emsp;&emsp;数组的元素是以逗号来分割，我们也可以使用逗号来合并数组：

``` perl
@numbers = ( 1, 3, ( 4, 5, 6 ) );
print "numbers = @numbers\n";  # 输出“numbers = 1 3 4 5 6”
```

也可以在数组中嵌入多个数组，并合并到主数组中：

``` perl
@odd  = ( 1, 3, 5 );
@even = ( 2, 4, 6 );
@numbers = ( @odd, @even );
print "numbers = @numbers\n";  # 输出“numbers = 1 3 5 2 4 6”
```

### 从列表中选择元素

&emsp;&emsp;一个列表可以当作一个数组使用，在列表后指定索引值可以读取指定的元素：

``` perl
$var = ( 5, 4, 3, 2, 1 )[4];
print "var的值为$var\n"  # 输出“var的值为1”
```

同样可以在数组中使用`..`来读取指定范围的元素：

``` perl
@list = ( 5, 4, 3, 2, 1 )[ 1 .. 3 ];
print "list的值 = @list\n";  # 输出“list的值 = 4 3 2”
```