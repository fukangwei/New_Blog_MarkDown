---
title: timeit模块
date: 2019-02-09 20:47:39
categories: Python语法
---
&emsp;&emsp;`timeit`模块提供了简单的方式来测量小段`Python`代码片，它有两种执行方式：命令行接口执行方式和`Python`程序代码可调的函数的方式。该模块避免了很多常见的度量时间的陷进。

### 基本的例子

&emsp;&emsp;下面例子演示使用`Command-Line Interface`比较三种不同的表达式：

``` python
$ python -m timeit '"-".join(str(n) for n in range(100))'
10000 loops, best of 3: 70.3 usec per loop
$ python -m timeit '"-".join([str(n) for n in range(100)])'
10000 loops, best of 3: 61 usec per loop
$ python -m timeit '"-".join(map(str, range(100)))'
10000 loops, best of 3: 56.5 usec per loop
```

同样可以用`Python Interface`来实现：

``` python
>>> import timeit
>>> timeit.timeit('"-".join(str(n) for n in range(100))', number=10000)
0.5236273538834694
>>> timeit.timeit('"-".join([str(n) for n in range(100)])', number=10000)
0.4634496997189608
>>> timeit.timeit('"-".join(map(str, range(100)))', number=10000)
0.4363641106430016
```

注意，模块只有用`Command-Line Interface`方式才会自动判断重复的次数。

### Python接口方式

&emsp;&emsp;该模块定义了三个实用函数和一个公共类：

- `timeit.timeit(stmt='pass', setup='pass', timer=<default timer>, number=1000000)`：用已给的语句创建一个`Timer`实例，参数分别是`stmt`(需要测量的语句或函数)、`setup`代码(初始化代码或构建环境的导入语句)、`timer`函数(计时函数)、`number`(每一次测量中语句被执行的次数)。注意，由于`timeit`正在执行语句，语句中如果存在返回值的话，会阻止`timeit`返回执行时间。`timeit`会取代原语句中的返回值。
- `timeit.repeat(stmt='pass', setup='pass', timer=<default timer>, repeat=3, number=1000000)`：用已给的语句创建一个`Timer`实例，参数`repeat`是重复测量的次数，其他参数与`timeit`类似。
- `timeit.default_timer()`：定义一个基于平台的默认的计时器。在`Windwos`系统，`time.clock`有微秒的精度，但是`time.time`只有`1/60`秒的精度；在`Unix`系统，`time.clock`有`1/100`秒的精度，而`time.time`的精度更高。
- `class timeit.Timer(stmt='pass', setup='pass', timer=<timer function>)`：计算小片代码段的类。构造函数需要的参数有`stmt`(需要测量的语句或函数)、`setup`代码(初始化代码或构建环境的导入语句)、`timer`函数(计时函数)，前两个参数的默认值都是`pass`。计时器函数依赖于具体平台，`stmt`和`setup`可以包括多个语句，以分号或者新行隔开。

&emsp;&emsp;第一次测量语句的执行时间，可以使用`timeit`方法。`repeat`方法相当于多次调用`timeit`，并返回一个结果集的列表。`stmt`和`setup`参数同样可以调用无参的对象，这将会在一个计时函数中嵌套调用它们，然后被`timeit`所执行，这样做的话会增加额外的开销。

### timeit

&emsp;&emsp;`timeit(number=1000000)`：计算语句执行`1000000`次的时间。该语句会先执行一次`setup`参数语句，然后计算`stmt`参数的语句执行`number`次的时间，返回值是以秒为单位的浮点数。`number`参数默认是`1百万`，`stmt`、`setup`和`timer`参数由`timeit.Timer`类的构造函数传递。

### repeat

&emsp;&emsp;`repeat(repeat=3, number=1000000)`：`repeat`相当于持续多次调用`timeit`方法，并将结果返回为一个列表。参数`repeat`指定重复的次数，`number`是传递给`timeit`方法的`number`参数。

### print_exc

&emsp;&emsp;`print_exc(file=None)`：输出计时代码中的`traceback`。典型用法如下：

``` python
t = Timer(...)  # 在“try/except”语句外使用
​
try:
    t.timeit(...)  # 或t.repeat(...)
except:
    t.print_exc()
```

这样做的好处是，可以将参数`file`直接定位到`traceback`，默认是`sys.stderr`。

### 命令行接口方式

&emsp;&emsp;当在命令行方式执行时，命令如下：

``` bash
python -m timeit [-n N] [-r N] [-s S] [-t] [-c] [-h] [statement ...]
```

详解如下：

选项 | 原型 | 含义
-----|------|-----
`-n N` | `--number=N` | 执行指定语句(段)的次数
`-r N` | `--repeat=N` | 重复测量的次数，默认是`3`次
`-s S` | `--setup=S`  | 执行时初始语句(默认是`pass`)
`-p`   | `--process`  | 测量进程时间而不是实际执行时间(使用`time.process_time`代替默认的`time.perf_counter`)
`-t`   | `--time`     | 使用`time.time`(默认除`Windows`外所有的操作系统)
`-c`   | `--clock`    | 使用`time.clock`(默认只在`Windows`操作系统上执行)
`-v`   | `--verbose`  | 打印原始的计时结果，输出更大精度的数值
`-h`   | `--help`     | 打印一个简短的用法信息并退出

### 详细的例子

&emsp;&emsp;在开始时设置初始化语句：

``` bash
$ python -m timeit -s 'text = "sample string"; char = "g"'  'char in text'
10000000 loops, best of 3: 0.0936 usec per loop
$ python -m timeit -s 'text = "sample string"; char = "g"'  'text.find(char)'
1000000 loops, best of 3: 0.4 usec per loop
```

使用`timeit`模块：

``` python
>>> import timeit
>>> timeit.timeit('char in text', setup='text = "sample string"; char = "g"')
0.1253384683015776
>>> timeit.timeit('text.find(char)', setup='text = "sample string"; char = "g"')
0.5727204384393936
```

同样可以用`Timer`类和它们的方法来实现：

``` python
>>> import timeit
>>> t = timeit.Timer('char in text', setup='text = "sample string"; char = "g"')
>>> t.timeit()
0.12571392650598057
>>> t.repeat()
[0.12860170469997456, 0.12690914937762443, 0.11270203329004858]
```

&emsp;&emsp;下面的例子演示如何测量多行表达式的执行时间，这里主要比较`hasattr vs try/except`来测试缺省或存在两种情况下的对象属性的时间消耗：

``` python
$ python -m timeit 'try:' '  str.__nonzero__' 'except AttributeError:' '  pass'
1000000 loops, best of 3: 1.86 usec per loop
$ python -m timeit 'if hasattr(str, "__nonzero__"): pass'
1000000 loops, best of 3: 1.22 usec per loop
$ python -m timeit 'try:' '  int.__nonzero__' 'except AttributeError:' '  pass'
1000000 loops, best of 3: 1.86 usec per loop
$ python -m timeit 'if hasattr(int, "__nonzero__"): pass'
1000000 loops, best of 3: 1.25 usec per loop
```

使用`timeit`模块：

``` python
>>> import timeit
>>> # 属性缺省的情况
>>> s = """ \
try:
    str.__nonzero__
except AttributeError:
    pass
"""
>>> timeit.timeit(stmt=s, number=100000)
0.08674410171410782
>>> s = "if hasattr(str, '__bool__'): pass"
>>> timeit.timeit(stmt=s, number=100000)
0.08676420027402543
>>> # 属性存在的情况
>>> s = """ \
try:
    int.__nonzero__
except AttributeError:
    pass
"""
>>> timeit.timeit(stmt=s, number=100000)
0.09908718327663735
>>> s = "if hasattr(int, '__bool__'): pass"
>>> timeit.timeit(stmt=s, number=100000)
0.028003708398131266
```

&emsp;&emsp;如果想让`timeit`模块测量你定义的函数，可以传递一个`setup`参数，该参数包括一个`import`语句：

``` python
def test():
    L = []
    for i in range(100):
        L.append(i)
​
if __name__ == '__main__':
    import timeit
    print(timeit.timeit("test()", setup="from __main__ import test"))
```