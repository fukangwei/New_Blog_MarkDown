---
title: importlib模块
categories: Python语法
date: 2018-12-27 17:54:10
---
### 基本用法

&emsp;&emsp;有时需要在程序运行的过程中，根据变量或者配置动态地决定导入哪个模块，使用`importlib`模块可以实现这一功能：<!--more-->

``` python
>>> import importlib
>>> math = importlib.import_module('math')
>>> math.sin(2)
0.9092974268256817
>>> mod = importlib.import_module('urllib.request')
>>> u = mod.urlopen('http://www.python.org')
```

`import_module`执行和`import`相同的步骤，但是返回模块对象。

### 文件结构

&emsp;&emsp;假设文件结构如下：

``` bash
a
│ -- a.py
│ -- __init__.py
b
│ -- b.py
│ -- __init__.py
│ -- c
     │ -- c.py
     │ -- __init__.py
```

&emsp;&emsp;`c.py`的内容如下：

``` python
args = {'a': 1}

class C:
    def c(self):
        print("I am C!")
```

&emsp;&emsp;在`a.py`中导入`c.py`中的对象：

``` python
import importlib

# 从“b module”导入“c module”中的“c.py”
params = importlib.import_module('b.c.c')  # 绝对导入
# params1 = importlib.import_module('.c.c', package='b')  # 相对导入
test = params.C()
print(params.args)
print(test.c())
```