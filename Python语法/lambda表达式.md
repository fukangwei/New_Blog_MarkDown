---
title: lambda表达式
categories: Python语法
date: 2018-12-28 09:50:03
---
### 基础用法

&emsp;&emsp;`lambda`表达式也叫`匿名函数`，实例如下：<!--more-->

``` python
sum = lambda arg1, arg2: arg1 + arg2
print("相加后的值为:", sum(10, 20))  # 结果为“相加后的值为: 30”
print("相加后的值为:", sum(20, 20))  # 结果为“相加后的值为: 40”
```

### 默认值

&emsp;&emsp;`lambda`可以设定默认值：

``` python
g = lambda x=0, y=0: x ** 2 + y ** 2
g(2, 3)  # 结果为13
g(2)  # 结果为4
g(y=3)  # 结果为9
```

### 无参数

&emsp;&emsp;`lambda`可以没有参数：

``` python
>>> a = lambda : "OK"
>>> print(a())
OK
```

### 多返回值

&emsp;&emsp;`lambda`可以有多个返回值：

``` python
>>> f = lambda x, y, z: (x + 1, y + 1, z + 1)
>>> f(1, 2, 3)
(2, 3, 4)
```

### lambda和for

&emsp;&emsp;请看下面的函数：

``` python
>>> f = [lambda x: x * i for i in range(4)]
>>> f[0](1)
3  # 1 * 3
>>> f[1](1)
3  # 1 * 3
>>> f[2](1)
3  # 1 * 3
>>> f[3](1)
3  # 1 * 3
>>> f[0](3)
9  # 3 * 3
>>> f[1](3)
9  # 3 * 3
>>> f[2](3)
9  # 3 * 3
>>> f[3](3)
9  # 3 * 3
```

上面的表达式展开如下：

``` python
def func():
    fs = []

    for i in range(4):
        def lam(x):
            return x * i

        fs.append(lam)

    return fs
```

当调用`func`时，每次循环都会将`lam`函数的地址存到`fs`中。因为每次循环的`lam`函数都未绑定`i`的值，所以直到循环结束，`i`的值为`3`，并将`lam`中所用到的`i`定为`3`。
&emsp;&emsp;没有参数的情况如下：

``` python
>>> f = [lambda: i * i for i in range(4)]
>>> f[0]()
9
>>> f[1]()
9
>>> f[2]()
9
>>> f[3]()
9
```