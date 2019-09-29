---
title: functools模块
categories: Python语法
abbrlink: b53bda20
date: 2019-01-09 09:57:39
---
&emsp;&emsp;`Python`的`functools`模块可以说主要是为函数式编程而设计，用于增强函数功能。

### functools.partial

&emsp;&emsp;用于创建一个偏函数，它用一些默认参数包装一个可调用对象，返回结果是可调用对象，并且可以像原始对象一样对待，这样可以简化函数调用。实际上`partial`相当于一个高阶函数，其大致的实现如下(实际在标准库中它是用`C`实现的)：

``` python
def partial(func, *args, **keywords):
    def newfunc(*fargs, **fkeywords):
        newkeywords = keywords.copy()
        newkeywords.update(fkeywords)
        return func(*(args + fargs), **newkeywords)

    newfunc.func = func
    newfunc.args = args
    newfunc.keywords = keywords
    return newfunc
```

声明如下：

``` python
urlunquote = functools.partial(urlunquote, encoding='latin1')
```

调用`urlunquote(args, *kargs)`时相，当于调用`urlunquote(args, *kargs, encoding='latin1')`。

``` python
from functools import partial
​
def add(a, b):
    return a + b
​
plus3 = partial(add, 3)
plus5 = partial(add, 5)
​
print("add(4,2) =", add(4, 2))
print("plus3(4) =", plus3(4))
print("plus3(7) =", plus3(7))
print("plus5(10) =", plus5(10))
```

执行结果：

``` python
add(4,2) = 6
plus3(4) = 7
plus3(7) = 10
plus5(10) = 15
```

### functool.update_wrapper

&emsp;&emsp;用`partial`包装的函数是没有`__name__`和`__doc__`，这使得依赖于这些属性的代码可能无法正常工作。`update_wrapper`可以拷贝被包装函数的`__name__`、`__module__`、`__doc__`和`__dict__`属性到新的封装函数中去，其实现也非常简单：

``` python
WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__doc__')
WRAPPER_UPDATES = ('__dict__',)
​
def update_wrapper(wrapper, wrapped, assigned=WRAPPER_ASSIGNMENTS, updated=WRAPPER_UPDATES):
    for attr in assigned:
        setattr(wrapper, attr, getattr(wrapped, attr))

    for attr in updated:
        getattr(wrapper, attr).update(getattr(wrapped, attr, {}))

    return wrapper
```

`update_wrapper`主要用在装饰器函数中，以确保被装饰的函数保留原来的属性：

``` python
from functools import update_wrapper
​
def wrap(func):
    def call_it(*args, **kwargs):
        """ wrap func: call_it """
        print('before call')
        return func(*args, **kwargs)
​
    return call_it
​
@wrap
def hello():
    """ say hello """
    print('hello world')
​
def wrap2(func):
    def call_it(*args, **kwargs):
        """ wrap func: call_it2 """
        print('before call')
        return func(*args, **kwargs)
​
    return update_wrapper(call_it, func)
​
@wrap2
def hello2():
    """ test hello """
    print('hello world2')
​
if __name__ == '__main__':
    hello()
    print(hello.__name__)
    print(hello.__doc__)
    hello2()
    print(hello2.__name__)
    print(hello2.__doc__)
```

执行结果：

``` python
before call
hello world
call_it
wrap func: call_it
before call
hello world2
hello2
test hello
```

### functool.wraps

&emsp;&emsp;`wraps`函数是为了在装饰器中方便地拷贝被装饰函数的签名，而对`update_wrapper`做的一个包装，其实现如下：

``` python
def wraps(wrapped, assigned=WRAPPER_ASSIGNMENTS, updated=WRAPPER_UPDATES):
    return partial(update_wrapper, wrapped=wrapped, assigned=assigned, updated=updated)
```

示例如下：

``` python
from functools import wraps
​
def wrap3(func):
    @wraps(func)
    def call_it(*args, **kwargs):
        print("calling", func.__name__)
        return func(*args, **kwargs)
​
    return call_it
​
@wrap3
def hello3():
    print("I am hello3")
​
hello3()
print(hello3.__name__)
```

执行结果：

``` python
calling hello3
I am hello3
hello3
```

### functools.reduce

&emsp;&emsp;在`Python 2`中等同于内建函数`reduce`，但是`Python 3`将`reduce`函数移除，只能使用`functools.reduce`。该函数的作用是将一个序列归纳为一个输出：

``` python
reduce(function, sequence, startValue)
```

使用示例如下：

``` python
from functools import reduce
​
def foo(x, y):
    return x + y
​
result = range(1, 10)
print(reduce(foo, result))  # 结果为45
print(reduce(foo, result, 10))  # 结果为55
```

### functools.cmp_to_key

&emsp;&emsp;在`list.sort`和内建函数`sorted`中都有一个`key`参数，该参数用来指定元素的某种属性进行比较。例如按字符串元素的长度进行比较：

``` python
>>> x = ['hello', 'abc', 'iplaypython.com']
>>> x.sort(key=len)
>>> x
['abc', 'hello', 'iplaypython.com']
```

也就是说排序时会先对每个元素调用`key`所指定的函数，然后再排序。同时，`sorted`和`list.sort`还提供了`cmp`参数来指定如何比较两个元素，但是`Python 3`移除了该参数。`cmp_to_key`函数就是用来将老式的比较函数转化为`key`函数。用到`key`参数的函数还有`sorted`、`min`、`max`、`heapq.nlargest`、`itertools.groupby`等。

### functools.total_ordering

&emsp;&emsp;这个一个类装饰器，如果一个类实现了`__lt__`、`__le__`、`__gt__`、`__ge__`这些方法中的至少一个，该装饰器会自动实现其他几个方法：

``` python
from functools import total_ordering
​
@total_ordering
class Student:
    def __init__(self, firstname, lastname):
        self.firstname = firstname
        self.lastname = lastname
​
    def __eq__(self, other):
        return ((self.lastname.lower(), self.firstname.lower())
                 == (other.lastname.lower(), other.firstname.lower()))
​
    def __lt__(self, other):
        return ((self.lastname.lower(), self.firstname.lower())
                 < (other.lastname.lower(), other.firstname.lower()))
​
print(dir(Student))
​
stu = Student("Huoty", "Kong")
stu2 = Student("Huoty", "Kong")
stu3 = Student("Qing", "Lu")
​
print(stu == stu2)
print(stu > stu3)
```

执行结果：

``` python
['__class__', '__delattr__', '__dict__',
'__dir__', '__doc__', '__eq__', '__format__',
'__ge__', '__getattribute__', '__gt__', '__hash__',
'__init__', '__init_subclass__', '__le__', '__lt__',
'__module__', '__ne__', '__new__', '__reduce__',
'__reduce_ex__', '__repr__', '__setattr__',
'__sizeof__', '__str__', '__subclasshook__', '__weakref__']
True
False
```

### functools.lru_cache

&emsp;&emsp;该装饰器用于缓存函数的调用结果，对于需要多次调用的函数，而且每次调用参数都相同，则可以用该装饰器缓存调用结果，从而加快程序运行：

``` python
from functools import lru_cache
​
@lru_cache(None)
def add(x, y):
    print("calculating: %s + %s" % (x, y))
    return x + y
​
print(add(1, 2))
print(add(1, 2))  # 直接返回缓存信息
print(add(2, 3))
```

执行结果：

``` python
calculating: 1 + 2
3
3
calculating: 2 + 3
5
```