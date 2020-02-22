---
title: contextlib模块
categories: Python语法
date: 2018-12-27 19:15:21
---
&emsp;&emsp;在`Python`中，读写文件这类资源时要特别注意，必须在使用完毕后正确关闭它们。其中一个方法是使用`try...finally`：<!--more-->

``` python
try:
    f = open('/path/to/file', 'r')
    f.read()
finally:
    if f:
        f.close()
```

`try...finally`非常繁琐，因此`Python`提供`with`语句允许我们非常方便地使用资源，而不必担心资源没有关闭，所以上面的代码可以简化为：

``` python
with open('/path/to/file', 'r') as f:
    f.read()
```

并不是只有`open`函数返回的`fp`对象才能使用`with`语句。实际上，任何对象只要正确实现了上下文管理，就可以用于`with`语句。
&emsp;&emsp;实现上下文管理是通过`__enter__`和`__exit__`这两个方法实现的。下面的`class`实现了这两个方法：

``` python
class Query(object):
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        print('Begin')
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        if exc_type:
            print('Error')
        else:
            print('End')

    def query(self):
        print('Query info about %s...' % self.name)

with Query('Bob') as q:
    q.query()
```

执行结果：

``` bash
Begin
Query info about Bob...
End
```

&emsp;&emsp;编写`__enter__`和`__exit__`仍然很繁琐，因此`Python`的标准库`contextlib`提供了更简单的写法，上面的代码可以改写如下：

``` python
from contextlib import contextmanager

class Query(object):
    def __init__(self, name):
        self.name = name

    def query(self):
        print('Query info about %s...' % self.name)

@contextmanager
def create_query(name):
    print('Begin')
    q = Query(name)
    yield q
    print('End')

with create_query('Bob') as q:
    q.query()
```

`contextmanager`是一个装饰器，由它修饰的方法会有两部分构成，中间由`yield`关键字分开。由此方法创建的上下文管理器，在代码块执行前，先执行`yield`上面的语句；在代码块执行后，再执行`yield`下面的语句。