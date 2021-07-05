---
title: functools模块
categories: Python语法
date: 2019-01-09 09:57:39
---
&emsp;&emsp;`functools`模块可以说主要是为函数式编程而设计，用于增强函数功能。<!--more-->

### functools.partial

&emsp;&emsp;代码实例：

``` python
from functools import partial

def add(a, b):
    return a + b

plus3 = partial(add, 3)
plus5 = partial(add, 5)

print("add(4, 2) =", add(4, 2))
print("plus3(4) =", plus3(4))
print("plus3(7) =", plus3(7))
print("plus5(10) =", plus5(10))
```

执行结果：

``` python
add(4, 2) = 6
plus3(4) = 7
plus3(7) = 10
plus5(10) = 15
```

### functools.reduce

&emsp;&emsp;该函数的作用是将一个序列归纳为一个输出：

``` python
from functools import reduce

def foo(x, y):
    return x + y

result = range(1, 10)
print(reduce(foo, result))  # 结果为45
print(reduce(foo, result, 10))  # 结果为55
```