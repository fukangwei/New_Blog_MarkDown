---
title: Python属性集合
categories: Python语法
date: 2019-02-06 12:35:53
---
### \_\_file\_\_

&emsp;&emsp;`__file__`就是当前脚本运行的路径：<!--more-->

``` python
import os
print(os.path.dirname(__file__))
```

- 按相对路径`python test.py`执行，则输出空目录。
- 按绝对路径`python D:\Fukangwei\test_python.py`执行，则得到的是目录`D:\Fukangwei`。

### \_\_all\_\_

&emsp;&emsp;`__all__`定义了当使用`from <module> import *`导入某个模块时，能够导出的符号。
&emsp;&emsp;在`foo.py`中，`__all__`明确地说明了可以导出符号`bar`和`baz`：

``` python
__all__ = ['bar', 'baz']

waz = 5
bar = 10

def baz():
    return 'baz'
```

&emsp;&emsp;测试代码如下：

``` python
from foo import *

print(bar)
print(baz())
print(waz)  # 该代码会抛出异常，因为“waz”并没有从模块中导出
```

&emsp;&emsp;需要注意的是，`__all__`只影响到了`from <module> import *`这种导入方式，对于`from <module> import <member>`并没有影响。

### \_\_slot\_\_

&emsp;&emsp;在`Python`中，当创建了一个类的实例后，还可以给该实例绑定任意新的属性和方法：

``` python
class Point(object):
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

p = Point(3, 4)
p.z = 5  # 绑定了一个新的属性
print(p.z)  # 输出“5”
print(p.__dict__)  # 输出“{'x': 3, 'y': 4, 'z': 5}”
```

这种动态绑定的功能虽然很有用，但它的代价是消耗了更多的内存。
&emsp;&emsp;为了不浪费内存，可以使用`__slots__`只给一个固定集合的属性分配空间：

``` python
class Point(object):
    __slots__ = ('x', 'y')  # 只允许使用x和y

    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

p = Point(3, 4)
p.z = 5  # 绑定了一个新的属性
```

执行结果：

``` python
AttributeError: 'Point' object has no attribute 'z'
```