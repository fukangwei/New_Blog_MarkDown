---
title: Perl引用
categories: Perl
abbrlink: 96a446fa
date: 2018-12-18 17:35:04
---
&emsp;&emsp;引用就是指针，`Perl`引用是一个标量类型可以指向变量、数组、哈希表甚至子程序，可以应用在程序的任何地方。<!--more-->

### 创建引用

&emsp;&emsp;定义变量时，在变量名前面加个`\`，就得到了这个变量的一个引用：

``` perl
$scalarref = \$foo;      # 标量变量引用
$arrayref  = \@ARGV;     # 列表的引用
$hashref   = \%ENV;      # 哈希的引用
$coderef   = \&handler;  # 子过程引用
$globref   = \*foo;      # GLOB句柄引用
```

在数组中，我们可以用匿名数组引用，使用`[]`定义：

``` perl
$aref = [1, "foo", undef, 13];
```

匿名数组的元素仍然可以是匿名数组，所以我们可以用这种方法构造任意维度的数组：

``` perl
my $aref = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
];
```

&emsp;&emsp;在哈希中，可以用匿名哈希引用，使用`{}`定义：

``` perl
$href = {APR => 4, AUG => 8};
```

也可以创建一个没有子程序名的匿名子程序引用：

``` perl
$coderef = sub {print "Runoob!\n"};
```

### 取消引用

&emsp;&emsp;可以根据不同的类型使用`$`、`@`或`%`来取消引用，实例如下：

``` perl
$var = 10;
$r = \$var;  # “$r”引用“$var”标量
print "$var为：", $$r, "\n";  # 输出本地存储的“$r”的变量值
​
@var = (1, 2, 3);
$r = \@var;  # “$r”引用“@var”数组
print "@var为：",  @$r, "\n";  # 输出本地存储的“$r”的变量值

%var = ('key1' => 10, 'key2' => 20);
$r = \%var;  # “$r”引用“%var”数组
print "%var为：", %$r, "\n";  # 输出本地存储的“$r”的变量值
```

执行结果：

``` bash
10为：10
1 2 3为：123
%var为：key110key220
```

&emsp;&emsp;如果不能确定变量类型，可以使用`ref`来判断，返回值列表如下。如果没有以下的值，则返回`false`：

- SCALAR
- ARRAY
- HASH
- CODE
- GLOB
- REF

示例如下：

``` perl
$var = 10;
$r = \$var;
print "r的引用类型：", ref($r), "\n";  # 输出“r的引用类型：SCALAR”

@var = ( 1, 2, 3 );
$r = \@var;
print "r的引用类型：", ref($r), "\n";  # 输出“r的引用类型：ARRAY”

%var = ( 'key1' => 10, 'key2' => 20 );
$r = \%var;
print "r的引用类型：", ref($r), "\n";  # 输出“r的引用类型：HASH”
```

### 循环引用

&emsp;&emsp;循环引用在两个引用相互包含时出现，你需要小心使用，不然会导致内存泄露：

``` perl
my $foo = 100;
$foo = \$foo;
# 输出“Value of foo is : REF(0x660dc8)”
print "Value of foo is : ", $$foo, "\n";
```

### 引用函数

&emsp;&emsp;函数引用格式为`\&`，调用引用函数格式为`& + 创建的引用名`：

``` perl
sub PrintHash {
    my (%hash) = @_;
​
    foreach $item (%hash) {
        print "元素：$item\n";
    }
}
​
%hash = ( 'name' => 'runoob', 'age' => 3 );

$cref = \&PrintHash;  # 创建函数的引用
&$cref(%hash);  # 使用引用调用函数
```

执行结果：

``` bash
元素：age
元素：3
元素：name
元素：runoob
```