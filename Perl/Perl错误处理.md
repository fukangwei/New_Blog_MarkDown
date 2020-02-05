---
title: Perl错误处理
categories: Perl
abbrlink: 701df9ed
date: 2018-12-18 07:06:34
---
### if语句

&emsp;&emsp;`if`语句可以判断语句的返回值：<!--more-->

``` perl
if ( open( DATA, $file ) ) {
    ...;
} else {
    die "Error: 无法打开文件 - $!";
}
```

变量`$!`返回了错误信息。也可以将以上代码简化一下：

``` perl
open(DATA, $file) || die "Error: 无法打开文件 - $!";
```

### unless函数

&emsp;&emsp;`unless`函数与`if`相反，只有在表达式返回`false`时才会执行：

``` perl
unless ( chdir("/etc") ) {
    die "Error: 无法打开目录 - $!";
}
```

`unless`语句在设置错误提醒时是非常有用的。也可以将以上代码简写为：

``` perl
die "Error: 无法打开目录!: $!" unless ( chdir("/etc") );
```

### 三元运算符

&emsp;&emsp;以下是一个三元运算符的简单实例：

``` perl
print( exists( $hash{value} ) ? '存在' : '不存在', "\n" );
```

以上实例使用了三元运算符来判断哈希的值是否存在。实例中包含了一个表达式和两个值，格式为`表达式 ? 值一 : 值二`。

### warn函数

&emsp;&emsp;`warn`函数用于触发一个警告信息，不会有其他操作，输出到`STDERR`(标准输出文件)，通常用于给用户提示：

``` perl
chdir('/etc') or warn "无法切换目录";
```

### die函数

&emsp;&emsp;`die`函数类似于`warn`，但它执行以后会退出，一般用作错误信息的输出：

``` perl
chdir('/etc') or die "无法切换目录";
```