---
title: random模块
categories: Python语法
abbrlink: 7fc559f6
date: 2018-12-28 09:00:41
---
&emsp;&emsp;Python中的`random`模块用于生成随机数。下面介绍一下`random`模块中最常用的几个函数。

### random.random

&emsp;&emsp;其原型为：

``` python
random.random()
```

用于生成一个`0`到`1`的随机符点数`n`(`0 <= n < 1.0`)。

### random.uniform

&emsp;&emsp;其原型为：

``` python
random.uniform(a, b)
```

用于生成一个指定范围内的随机符点数，两个参数其中一个是上限，另一个是下限。在终端输入：

``` python
import random
​
print(random.uniform(10, 20))
print(random.uniform(20, 20))
```

执行结果：

``` bash
18.7356606526
20.0
```

### random.randint

&emsp;&emsp;其原型为：

``` python
random.randint(a, b)
```

用于生成一个指定范围内的整数。其中参数`a`是下限，参数`b`是上限，生成的随机数`n`(`a <= n <= b`)。

``` python
print(random.randint(12, 20))  # 生成的随机数n(12 <= n <= 20)
print(random.randint(20, 20))  # 结果永远是20
print(random.randint(20, 10))  # 该语句是错误的，下限必须小于上限
```

### random.randrange

&emsp;&emsp;其原型为：

``` python
random.randrange([start], stop[, step])
```

从指定范围内，按指定基数递增的集合中获取一个随机数。例如`random.randrange(10, 100, 2)`结果相当于从`[10, 12, 14, 16, ... 96, 98]`序列中获取一个随机数。`random.randrange(10, 100, 2)`在结果上与`random.choice(range(10, 100, 2)`等效。

### random.choice

&emsp;&emsp;`random.choice`用于从序列中获取一个随机元素，其原型为：

``` python
random.choice(sequence)
```

参数`sequence`表示一个有序类型。这里需要注意：`sequence`在`python`不是一种特定的类型，而是泛指一系列的类型，`list`、`tuple`、`字符串`都属于`sequence`。下面是使用`choice`的一些例子：

``` python
print(random.choice("学习Python"))
print(random.choice(["JGood", "is", "a", "handsome", "boy"]))
print(random.choice(("Tuple", "List", "Dict")))
```

### random.shuffle

&emsp;&emsp;其原型为：

``` python
random.shuffle(x[, random])
```

用于将一个列表中的元素打乱。在终端输入：

``` python
p = ["Python", "is", "powerful", "simple", "and so on..."]
random.shuffle(p)
print(p)
```

执行结果：

``` bash
['Python', 'and so on...', 'simple', 'powerful', 'is']
```

### random.sample

&emsp;&emsp;其原型为：

``` python
random.sample(sequence, k)
```

从指定序列中随机获取指定长度的片段。`sample`函数不会修改原有序列：

``` python
list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
slice = random.sample(list, 5)  # 从list中随机获取5个元素，作为一个片段返回
print(slice)
print(list)  # 原有序列并没有改变
```