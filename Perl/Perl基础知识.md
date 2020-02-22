---
title: Perl基础知识
categories: Perl
date: 2018-12-17 22:10:30
---
### Perl基础

&emsp;&emsp;`Perl`是`Practical Extraction and Report Language`的缩写，可翻译为`实用报表提取语言`。`Perl`是高级、通用、直译式、动态的程序语言。<!--more-->
&emsp;&emsp;`Perl`借用了`C`、`sed`、`awk`、`shell`脚本以及很多其他编程语言的特性，最重要的特性是内部集成了正则表达式的功能，以及巨大的第三方代码库`CPAN`。第一个`Perl`程序如下：

``` perl
print "Hello, World!\n";
```

将其保存为`test.pl`文件，使用`perl test.pl`执行。`print`也可以使用括号来输出字符串：

``` perl
print("Hello, world\n");
```

&emsp;&emsp;`Perl`是一种功能丰富的计算机程序语言，运行在超过`100`种计算机平台上，适用广泛，从大型机到便携设备，从快速原型创建到大规模可扩展开发。`Perl`语言的应用范围很广，除`CGI`以外，`Perl`被用于图形编程、系统管理、网络编程、金融、生物以及其他领域。
&emsp;&emsp;`Perl`与脚本语言一样，不需要编译器和链接器来运行代码，你要做的只是写出程序并告诉`Perl`来运行而已。`Perl`提供脚本语言(如`sed`和`awk`)的所有功能，还具有它们所不具备的很多功能，同时还支持`sed`到`Perl`以及`awk`到`Perl`的翻译器。简而言之，`Perl`像`C`一样强大，像`awk`、`sed`等脚本描述语言一样方便。
&emsp;&emsp;`Perl`的优点如下：

- 相比于`C`、`Pascal`这样的高级语言，`Perl`语言直接提供泛型变量、动态数组、`Hash`表等更加便捷的编程元素。
- `Perl`具有动态语言的强大灵活的特性，并且还从`C/C++`、`Basic`、`Pascal`等语言中分别借鉴了语法规则，从而提供了许多冗余语法。
- 在统一变量类型和掩盖运算细节方面，`Perl`做得比其他高级语言(例如`Python`)更为出色。
- 由于从其他语言大量借鉴了语法，使得从其他编程语言转到`Perl`语言的程序员可以迅速上手写程序并完成任务，这使得`Perl`语言是一门容易用的语言。
- `Perl`是可扩展的，我们可以通过`CPAN`(`the Comprehensive Perl Archive Network`，意思是`全面的Perl存档网络`)中心仓库找到很多我们需要的模块。
- `Perl`的`mod_perl`的模块允许`Apache`服务器使用`Perl`解释器。

&emsp;&emsp;`Perl`的缺点如下：

- 也正是因为`Perl`的灵活性和过度的冗余语法，也因此获得了`仅写(write-only)`的坏名声，因为`Perl`程序可以写得很随意(例如变量不经声明就可以直接使用)，可能少写一些字母就会得到意想不到的结果(而不报错)。许多`Perl`程序的代码令人难以阅读，实现相同功能的程序代码长度可以相差十倍百倍，这就令程序的维护者(甚至是编写者)难以维护。
- 同样的，因为`Perl`这样随意的特点，可能会导致一些`Perl`程序员遗忘语法，以至于不得不经常查看`Perl`手册。建议的解决方法是在程序里使用`use strict;`以及`use warnings;`，并统一代码风格。`Perl`同样可以将代码书写得像`Python`或`Ruby`等语言一样优雅。
- 很多时候，`perl.exe`进程会占用很多的内存空间。

### 注释

&emsp;&emsp;使用注释使你的程序易读，这是好的编程习惯。`perl`注释的方法为在语句的开头使用字符`#`：

``` perl
# 这一行是perl中的注释
```

`perl`也支持多行注释，最常用的方法是使用`POD`(`Plain Old Documentations`)：

``` perl
print "Hello, world\n"; # 这是一个单行注释

=pod 注释
这是一个多行注释
这是一个多行注释
这是一个多行注释
这是一个多行注释
=cut
```

注意，`=pod`和`=cut`只能在行首。这种注释以`=`开头，以`=cut`结尾，并且开头的`=`后面要紧接一个字符。

### 单引号和双引号

&emsp;&emsp;`perl`输出字符串可以使用`'`和`"`：

``` perl
print "Hello, world\n"; # 双引号，输出“Hello, world”
print 'Hello, world\n'; # 单引号，输出“Hello, world\n”
```

可以看出，双引号中的`\n`输出了换行，而单引号没有。双引号可以正常解析一些转义字符与变量，而单引号无法解析，原样输出：

``` perl
$a = 10;
print "a = $a\n"; # 输出“a = 10”
print 'a = $a\n'; # 输出“a = $a\n”
```

### 转义字符

&emsp;&emsp;如果我们需要输出一个特殊的字符，可以使用反斜线`\`来转义，例如输出美元符号`$`：

``` perl
$result = "菜鸟教程 \"runoob\"";
print "$result\n"; # 输出“菜鸟教程 "runoob"”
print "\$result\n"; # 输出“$result”
```