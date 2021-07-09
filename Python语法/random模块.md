---
title: random模块
categories: Python语法
date: 2018-12-28 09:00:41
---
&emsp;&emsp;`random`模块用于生成随机数。<!--more-->

### random.random

&emsp;&emsp;生成一个`0`到`1`的随机浮点数`n`(`0 <= n < 1.0`)：

``` python
random.random()
```

### random.uniform

&emsp;&emsp;生成一个指定范围内的随机浮点数：

``` python
random.uniform(a, b)
```

&emsp;&emsp;代码实例：

``` python
import random

print(random.uniform(10, 20))  # 生成的随机数n(10 <= n <= 20)
print(random.uniform(20, 20))  # 结果永远是20
```

执行结果：

``` bash
18.7356606526
20.0
```

### random.randint

&emsp;&emsp;生成一个指定范围内的整数`n`(`a <= n <= b`)：

``` python
random.randint(a, b)
```

&emsp;&emsp;代码实例：

``` python
import random

print(random.randint(12, 20))  # 生成的随机数n(12 <= n <= 20)
print(random.randint(20, 20))  # 结果永远是20
```

### random.randrange

&emsp;&emsp;在指定的范围内，从一个递增的集合中获取一个随机数：

``` python
random.randrange([start], stop[, step])
```

`random.randrange(10, 100, 2)`相当于从序列`[10, 12, 14, 16, ... 96, 98]`中获取一个随机数。

### random.choice

&emsp;&emsp;从序列中获取一个随机元素：

``` python
random.choice(sequence)
```

&emsp;&emsp;代码实例：

``` python
import random

print(random.choice(["JGood", "is", "a", "handsome", "boy"]))
print(random.choice(("Tuple", "List", "Dict")))
```

### random.shuffle

&emsp;&emsp;将一个列表中的元素打乱：

``` python
random.shuffle(x[, random])
```

&emsp;&emsp;代码实例：

``` python
import random

p = ["Python", "is", "powerful", "simple", "and so on..."]
random.shuffle(p)
print(p)
```

### random.sample

&emsp;&emsp;在指定序列中，随机获取指定长度的片段：

``` python
random.sample(sequence, k)
```

&emsp;&emsp;`sample`函数不会修改原有序列：

``` python
import random

list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
slice = random.sample(list, 5)  # 从list中随机获取5个元素，作为一个片段返回
print(slice)
print(list)  # 原有序列并没有改变
```