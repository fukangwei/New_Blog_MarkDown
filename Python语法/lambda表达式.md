---
title: lambda表达式
categories: Python语法
abbrlink: 330a6169
date: 2018-12-28 09:50:03
---
&emsp;&emsp;`lambda`表达式也叫`匿名函数`，其语法如下：<!--more-->

``` python
lambda [arg1 [, arg2, ..., argn]]: expression
```

实例如下：

``` python
sum = lambda arg1, arg2: arg1 + arg2
print("相加后的值为:", sum(10, 20))  # 结果为“相加后的值为: 30”
print("相加后的值为:", sum(20, 20))  # 结果为“相加后的值为: 40”
```

&emsp;&emsp;`lambda`表达式的作用：`Python`写一些执行脚本时，使用`lambda`就可以省下定义函数的过程。例如我们只是写一个简单的脚本来管理服务器时间，就不需要专门定义一个函数然后再写调用，使用`lambda`就可以使得代码更加精简。对于一些比较抽象并且整个程序执行下来只需要调用一两次函数，有时候给函数起个名字也是比较头疼的问题。使用`lambda`就不需要考虑命名的问题。简化代码的可读性，由于普通的函数阅读经常要跳到开头`def`定义部分，使用`lambda`就可以省去这样的步骤。
&emsp;&emsp;`lambda`函数也是可以使用`关键字参数`进行参数传递：

``` python
g = lambda x, y: x ** 2 + y ** 2
g(2, 3)  # 结果为13
g(y=3, x=2)  # 结果为13
```

&emsp;&emsp;`lambda`函数也可以设定默认值：

``` python
g = lambda x=0, y=0: x ** 2 + y ** 2
g(2, 3)  # 结果为13
g(2)  # 结果为4
g(y=3)  # 结果为9
```

&emsp;&emsp;`lambda`函数无参数的情况如下：

``` python
>>> a = lambda : "OK"
>>> print(a())
OK
```

&emsp;&emsp;当`lambda`函数有多个返回值时，用元祖形式返回：

``` python
>>> f = lambda x, y, z: (x + 1, y + 1, z + 1)
>>> f(1, 2, 3)
(2, 3, 4)
```

---

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
​
        fs.append(lam)

    return fs
```

当调用`func`时，每循环一次，将`lam`函数的地址存到`fs`中。因为在每次循环中`lam`函数都未绑定`i`的值，所以直到循环结束，`i`的值为`3`，并将`lam`中所用到的`i`值定为`3`。因此真正调用(例如`f[0](3)`)时，`i`值保持不变(为`3`)。
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