---
title: Perl哈希
categories: Perl
date: 2018-12-20 14:49:22
---
### 哈希变量

&emsp;&emsp;如果要访问哈希值，可以使用`$`加上`{key}`格式：<!--more-->

``` perl
%data = ( 'google', 45, 'runoob', 30, 'taobao', 40 );
print "\$data{'google'} = $data{'google'}\n";  # 输出“$data{'google'} = 45”
print "\$data{'runoob'} = $data{'runoob'}\n";  # 输出“$data{'runoob'} = 30”
print "\$data{'taobao'} = $data{'taobao'}\n";  # 输出“$data{'taobao'} = 40”
```

### 创建哈希

&emsp;&emsp;创建哈希可以通过以下两种方式：
&emsp;&emsp;1. 为每个`key`设置`value`：

``` perl
$data{'google'} = 'google.com';
$data{'runoob'} = 'runoob.com';
$data{'taobao'} = 'taobao.com';
```

&emsp;&emsp;2. 通过列表设置：列表中第一个元素为`key`，第二个为`value`：

``` perl
%data = ('google', 'google.com', 'runoob', 'runoob.com', 'taobao', 'taobao.com');
```

也可以使用`=>`符号来设置`key/value`：

``` perl
%data = ('google'=>'google.com', 'runoob'=>'runoob.com', 'taobao'=>'taobao.com');
```

以下实例是上面实例的变种，使用`-`来代替引号：

``` perl
%data = (-google=>'google.com', -runoob=>'runoob.com', -taobao=>'taobao.com');
```

使用这种方式`key`不能出现空格，读取元素方式为：

``` perl
$val = $data{-google}
$val = $data{-runoob}
```

### 读取哈希值

&emsp;&emsp;将哈希值提取到数组的语法格式：

``` perl
@{key1, key2}
```

示例如下：

``` perl
%data = (-taobao => 45, -google => 30, -runoob => 40);
@array = @data{-taobao, -runoob};
print "Array：@array\n"; # 输出“Array：45 40”
```

### 读取哈希的key和value

&emsp;&emsp;可以使用`keys`函数读取哈希所有的键：

``` perl
keys %HASH
```

该函数返回哈希的所有`key`的数组：

``` perl
%data = ('google'=>'google.com', 'runoob'=>'runoob.com', 'taobao'=>'taobao.com');
@names = keys %data;
print "$names[0]\n";  # 输出“google”
print "$names[1]\n";  # 输出“taobao”
print "$names[2]\n";  # 输出“runoob”
```

类似的，可以使用`values`函数来读取哈希所有的值：

``` perl
values %HASH
```

该函数返回哈希的所有`value`的数组：

``` perl
%data = ('google'=>'google.com', 'runoob'=>'runoob.com', 'taobao'=>'taobao.com');
@urls = values %data;
print "$urls[0]\n";  # 输出“google.com”
print "$urls[1]\n";  # 输出“runoob.com”
print "$urls[2]\n";  # 输出“taobao.com”
```

### 检测元素是否存在

&emsp;&emsp;如果在哈希中读取不存在的`key/value`，则会返回`undefined`值，且在执行时会有警告提醒。为了避免这种情况，可以使用`exists`函数来判断`key`是否存在：

``` perl
%data = ('google' => 'google.com', 'runoob' => 'runoob.com', 'taobao' => 'taobao.com');

if ( exists( $data{'facebook'} ) ) {
    print "facebook的网址为$data{'facebook'} \n";
} else {
    print "facebook键不存在\n";  # 输出“facebook键不存在”
}
```

### 获取哈希大小

&emsp;&emsp;哈希大小为元素的个数，我们可以通过先获取`key`或`value`的所有元素数组，再计算数组元素多少来获取哈希的大小：

``` perl
%data = ('google' => 'google.com', 'runoob' => 'runoob.com', 'taobao' => 'taobao.com');

@keys = keys %data;
$size = @keys;
print "1 - 哈希大小：$size\n";  # 输出“1 - 哈希大小：3”
@values = values %data;
$size   = @values;
print "2 - 哈希大小：$size\n";  # 输出“2 - 哈希大小：3”
```

### 哈希中添加或删除元素

&emsp;&emsp;添加`key/value`可以通过简单的赋值来完成，但是删除哈希元素则需要使用`delete`函数：

``` perl
%data = ('google' => 'google.com', 'runoob' => 'runoob.com', 'taobao' => 'taobao.com');

@keys = keys %data;
$size = @keys;
print "1 - 哈希大小：$size\n";  # 输出“1 - 哈希大小：3”
# 添加元素
$data{'facebook'} = 'facebook.com';
@keys = keys %data;
$size = @keys;
print "2 - 哈希大小：$size\n";  # 输出“2 - 哈希大小：4”
# 删除哈希中的元素
delete $data{'taobao'};
@keys = keys %data;
$size = @keys;
print "3 - 哈希大小：$size\n";  # 输出“3 - 哈希大小：3”
```