---
title: Python属性集合
categories: Python语法
abbrlink: '3565e905'
date: 2019-02-06 12:35:53
---
### \_\_file\_\_属性

&emsp;&emsp;在`Python`项目中，经常需要获取文件的路径。通常会见到如下的语句：<!--more-->

``` python
import os
os.path.dirname(__file__)
```

这里的`__file__`其实就是当前脚本运行的路径。但也会分不同的情况：

- 按相对路径`./test.py`来执行，则打印得到的是相对路径。
- 按绝对路径执行，则得到的是绝对路径。
- 按用户目录来执行(`~/practice/test.py`)，则得到的也是绝对路径。

所以为了得到绝对路径，我们需要使用`os.path.realpath(__file__)`。
&emsp;&emsp;如果在交互式环境中，则会爆出异常，因为此时`__file__`并未生成：

``` python
In [1]: import os
In [2]: print os.path.dirname(__file__)
----------------------------------------------
NameError    Traceback (most recent call last)
<ipython-input-3-b4616cfaa58d> in <module>()
----> 1 print os.path.dirname(__file__)
​
NameError: name '__file__' is not defined
```

### \_\_all\_\_属性

&emsp;&emsp;经常在`__init__.py`文件中看到`__all__`变量，它是一个`string`元素组成的`list`变量，定义了当你使用`from <module> import *`导入某个模块时能够导出的符号(可以是变量、函数、类等)。
&emsp;&emsp;在文件`foo.py`中，`__all__`明确地说明了可以导出了符号`bar`和`baz`：

``` python
__all__ = ['bar', 'baz']
​
waz = 5
bar = 10
​
def baz():
    return 'baz'
```

测试代码如下：

``` python
from foo import *
​
print(bar)
print(baz())
print(waz)  # 该代码会抛出异常，因为“waz”并没有从模块中导出
```

&emsp;&emsp;需要注意的是，`__all__`只影响到了`from <module> import *`这种导入方式，对于`from <module> import <member>`并没有影响，仍然可以从外部导入。

### \_\_slot\_\_属性

&emsp;&emsp;在`Python`中，我们在定义类的时候可以定义属性和方法。当我们创建了一个类的实例后，还可以给该实例绑定任意新的属性和方法：

``` python
class Point(object):
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
​
p = Point(3, 4)
p.z = 5  # 绑定了一个新的属性
print(p.z)  # 输出“5”
print(p.__dict__)  # 输出“{'x': 3, 'y': 4, 'z': 5}”
```

我们创建了实例`p`之后，给它绑定了一个新的属性`z`，这种动态绑定的功能虽然很有用，但它的代价是消耗了更多的内存。
&emsp;&emsp;为了不浪费内存，可以使用`__slots__`来告诉`Python`只给一个固定集合的属性分配空间：

``` python
class Point(object):
    __slots__ = ('x', 'y')  # 只允许使用x和y
​
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
​
p = Point(3, 4)
p.z = 5  # 绑定了一个新的属性
```

执行结果：

``` python
AttributeError: 'Point' object has no attribute 'z'
```