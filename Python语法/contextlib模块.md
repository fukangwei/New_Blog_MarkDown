---
title: contextlib模块
categories: Python语法
date: 2018-12-27 19:15:21
---
&emsp;&emsp;上下文管理是通过`__enter__`和`__exit__`这两个方法实现的。<!--more-->
&emsp;&emsp;下面的`class`实现了这两个方法：

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

&emsp;&emsp;编写`__enter__`和`__exit__`仍然很繁琐，因此`Python`的标准库`contextlib`提供了更简单的写法。
&emsp;&emsp;上面的代码可以改写如下：

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

&emsp;&emsp;`contextmanager`是一个装饰器，在代码块执行前，先执行`yield`上面的语句；在代码块执行后，再执行`yield`下面的语句。