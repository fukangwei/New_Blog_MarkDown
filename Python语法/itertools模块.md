---
title: itertools模块
categories: Python语法
date: 2019-02-10 20:31:27
---
&emsp;&emsp;`itertools`用于创建自定义的迭代器。<!--more-->

### 无限迭代器

#### count

&emsp;&emsp;`count`会返回从传入的起始参数开始的均匀间隔的数值：

``` python
from itertools import count

for i in count(18):
    if i > 20:
        break
    else:
        print(i)
```

执行结果：

``` python
18
19
20
```

&emsp;&emsp;另一种控制无限迭代器输出的方式是使用`itertools`的子模块`islice`：

``` python
from itertools import islice, count

# 迭代了5次之后停止
for i in islice(count(10), 5):
    print(i)
```

执行结果：

``` python
10
11
12
13
14
```

#### cycle

&emsp;&emsp;`cycle`允许你创建一个能在一组值间无限循环的迭代器：

``` python
from itertools import cycle

count = 0
for item in cycle('XYZ'):
    if count > 4:
        break

    print(item)
    count += 1
```

执行结果：

``` python
X
Y
Z
X
Y
```

&emsp;&emsp;也可以用`next`函数对`itertools`创建的迭代器进行循环：

``` python
>>> polys = ['triangle', 'square', 'pentagon', 'rectangle']
>>> iterator = cycle(polys)
>>> next(iterator)
'triangle'
>>> next(iterator)
'square'
>>> next(iterator)
'pentagon'
>>> next(iterator)
'rectangle'
>>> next(iterator)
'triangle'
>>> next(iterator)
'square'
```

#### repeat

&emsp;&emsp;`repeat`会一遍遍地返回传入的对象，直至永远，除非设定了次数：

``` python
>>> from itertools import repeat
>>> repeat(5, 2)
repeat(5, 2)
>>> iterator = repeat(5, 2)
>>> next(iterator)
5
>>> next(iterator)
5
>>> next(iterator)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

### 可终止的迭代器

#### accumulate

&emsp;&emsp;`accumulate`将返回累计计算的结果，默认是累计求和：

``` python
>>> from itertools import accumulate
>>> list(accumulate(range(10)))
[0, 1, 3, 6, 10, 15, 21, 28, 36, 45]
```

&emsp;&emsp;累计相乘的方法如下：

``` python
>>> import operator
>>> list(accumulate(range(1, 5), operator.mul))
[1, 2, 6, 24]
```

#### compress

&emsp;&emsp;`compress`用于过滤可迭代对象：

``` python
>>> from itertools import compress
>>> letters = 'ABCDEFG'
>>> bools = [True, False, True, True, False]
>>> list(compress(letters, bools))
['A', 'C', 'D']
```

#### starmap

&emsp;&emsp;`starmap`能够创建一个用传入的函数和可迭代对象计算的迭代器：

``` python
from itertools import starmap

def add(a, b):
    return a + b

for item in starmap(add, [(2, 3), (4, 5)]):
    print(item)
```

执行结果：

``` python
5
9
```

#### zip_longest

&emsp;&emsp;`zip_longest`用于将两个可迭代对象配对。如果可迭代对象的长度不同，也可以传入填充值：

``` python
from itertools import zip_longest

for item in zip_longest('ABCD', 'xy', fillvalue='BLANK'):
    print(item)
```

执行结果：

``` python
('A', 'x')
('B', 'y')
('C', 'BLANK')
('D', 'BLANK')
```

### 组合生成器

#### combinations

&emsp;&emsp;`combinations`能够让你通过有一定长度的可迭代对象创建一个迭代器：

``` python
>>> from itertools import combinations
>>> list(combinations('WXYZ', 2))
[('W', 'X'), ('W', 'Y'), ('W', 'Z'), ('X', 'Y'), ('X', 'Z'), ('Y', 'Z')]
```

&emsp;&emsp;为了使输出结果更具可读性，可以遍历迭代器将元组组合成字符串：

``` python
from itertools import combinations

for item in combinations('WXYZ', 2):
    print(''.join(item))
```

执行结果：

``` python
WX
WY
WZ
XY
XZ
YZ
```

#### product

&emsp;&emsp;`product`用于根据一系列输入的可迭代对象计算笛卡尔积：

``` python
>>> from itertools import product
>>> arrays = [(-1,1), (-3,3), (-5,5)]
>>> cp = list(product(*arrays))
>>> cp
[(-1, -3, -5),
 (-1, -3, 5),
 (-1, 3, -5),
 (-1, 3, 5),
 (1, -3, -5),
 (1, -3, 5),
 (1, 3, -5),
 (1, 3, 5)]
```