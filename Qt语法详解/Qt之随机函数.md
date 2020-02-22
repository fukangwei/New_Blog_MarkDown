---
title: Qt之随机函数
categories: Qt语法详解
date: 2019-01-02 17:41:50
---
&emsp;&emsp;其头文件为`QTime`，首先是初始化随机种子函数：<!--more-->

``` cpp
qsrand ( QTime ( 0, 0, 0 ).secsTo ( QTime::currentTime() ) );
```

接着就可以使用随机函数`qrand`。`qrand`理论上返回`0`到`RAND_MAX`间的值，如果要返回`0`至`n`间的值，则使用语句：

``` cpp
qrand() % n;
```

如果要返回`a`至`b`间的值，则使用如下语句：

``` cpp
a + qrand() % ( b - a );
```

完整的代码示例如下：

``` cpp
qsrand ( QTime ( 0, 0, 0 ).secsTo ( QTime::currentTime() ) );
int n = qrand();
```

还有一个简单方法：

``` cpp
qsrand ( time ( NULL ) );
int n = qrand();
```

这`2`句不一定要连着，初始化随机种子函数可以在程序开始时执行。如果`2`句连在一起，并且又同时出现在`for`循环中，就容易产生相同的随机数。