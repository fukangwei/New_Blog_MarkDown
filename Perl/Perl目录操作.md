---
title: Perl目录操作
categories: Perl
abbrlink: 74aa2817
date: 2018-12-18 07:52:52
---
&emsp;&emsp;以下列出了一些操作目录的标准函数：

函数                      | 作用
--------------------------|-------
`opendir DIRHANDLE, EXPR` | 打开目录
`readdir DIRHANDLE`       | 读取目录
`rewinddir DIRHANDLE`     | 定位指针到开头
`telldir DIRHANDLE`       | 返回目录的当前位置
`seekdir DIRHANDLE, POS`  | 定位指定到目录的`POS`位置
`closedir DIRHANDLE`      | 关闭目录

### 显示所有的文件

&emsp;&emsp;显示目录下的所有文件，以下实例使用了`glob`操作符：

``` perl
$dir = "/tmp/*";
my @files = glob($dir);  # 显示“/tmp”目录下的所有文件

foreach (@files) {
    print $_ . "\n";
}
​
$dir = "/tmp/*.c";
@files = glob($dir);  # 显示“/tmp”目录下所有以“.c”结尾的文件

foreach (@files) {
    print $_ . "\n";
}
​
$dir = "/tmp/.*";
@files = glob($dir);  # 显示所有隐藏文件

foreach (@files) {
    print $_ . "\n";
}
​
$dir = "/tmp/* /home/*";  # 显示“/tmp”和“/home”目录下的所有文件
@files = glob($dir);

foreach (@files) {
    print $_ . "\n";
}
```

以下实例可以列出当前目录下的所有文件：

``` perl
opendir( DIR, '.' ) or die "无法打开目录, $!";
​
while ( $file = readdir DIR ) {
    print "$file\n";
}
​
closedir DIR;
```

如果要显示`/tmp`目录下所有以`.c`结尾的文件，可以使用以下代码：

``` perl
opendir( DIR, '.' ) or die "无法打开目录, $!";
​
foreach ( sort grep( /^.*\.c$/, readdir(DIR) ) ) {
    print "$_\n";
}
​
closedir DIR;
```

### 创建一个新目录

&emsp;&emsp;可以使用`mkdir`函数来创建一个新目录，执行前你需要有足够的权限来创建目录：

``` perl
$dir = "/tmp/perl";
​
# 在“/tmp”目录下创建perl目录
mkdir($dir) or die "无法创建$dir目录, $!";
print "目录创建成功\n";
```

### 删除目录

&emsp;&emsp;可以使用`rmdir`函数来删除目录，执行该操作需要有足够权限。注意，要删除的目录必须的空目录。

``` perl
$dir = "/tmp/perl";

# 删除“/tmp”目录下的perl目录
rmdir( $dir ) or die "无法删除$dir目录, $!";
print "目录删除成功\n";
```

### 切换目录

&emsp;&emsp;可以使用`chdir`函数来切换当期目录，执行该操作需要有足够权限：

``` perl
$dir = "/home";
​
# 将当前目录移动到“/home”目录下
chdir($dir) or die "无法切换目录到 $dir , $!";
print "你现在所在的目录为$dir\n";
```