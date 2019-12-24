---
title: Perl之sort函数
categories: Perl
abbrlink: b32c6fe3
date: 2018-12-18 06:52:52
---
&emsp;&emsp;最简单的用法如下：

``` perl
my @arr = ( 3, 2, 8, 6 );

foreach ( sort { $a <=> $b } @arr ){
    print $_, "\n";
}
```

执行结果：

``` bash
2
3
6
8
```

&emsp;&emsp;将`hash`表的`hash value`按照`ASCII`码顺序进行排序：

``` perl
my %hash = ( 'a' => 2, 'b' => 3, 'c' => 11 );
​
foreach my $key ( sort { $hash{$a} cmp $hash{$b} } keys %hash ) {
    print $key, " => ", $hash{$key}, "\n";
}
```

执行结果：

``` bash
c => 11
a => 2
b => 3
```

&emsp;&emsp;将hash表的`hash value`按照数字顺序从小到大进行排序：

``` perl
my %hash = ( 'a' => 2, 'b' => 3, 'c' => 11 );
​
foreach my $key ( sort { $hash{$a} <=> $hash{$b} } keys %hash ) {
    print $key, " => ", $hash{$key}, "\n";
}
```

执行结果：

``` bash
a => 2
b => 3
c => 11
```

按照数字顺序从大到小进行排序：

``` perl
my %hash = ( 'a' => 2, 'b' => 3, 'c' => 11 );
​
foreach my $key ( sort { $hash{$b} <=> $hash{$a} } keys %hash ) {
    print $key, " => ", $hash{$key}, "\n";
}
```

执行结果：

``` bash
c => 11
b => 3
a => 2
```