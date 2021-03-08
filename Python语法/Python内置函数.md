---
title: Python内置函数
categories: Python语法
date: 2019-02-24 10:42:11
---
### chr

&emsp;&emsp;`chr`用一个范围在`range(256)`内的(就是`0`至`255`)整数作参数，返回一个对应的字符：<!--more-->

``` python
chr(i)
```

参数`i`可以是`10`进制也可以是`16`进制的形式的数字。

``` python
print(chr(0x30), chr(0x31), chr(0x61))  # 十六进制
print(chr(48), chr(49), chr(97))  # 十进制
```

执行结果：

``` python
0 1 a
0 1 a
```

### ord

&emsp;&emsp;`ord`以一个字符(长度为`1`的字符串)作为参数，返回对应的`ASCII`数值：

``` python
ord(c)
```

参数`c`是字符。

``` python
>>> ord('a')
97
>>> ord('b')
98
>>> ord('c')
99
```

### len

&emsp;&emsp;`len`返回对象(字符、列表、元组等)长度或项目个数：

``` python
len(s)
```

参数`s`是一个对象。

``` python
str = "runoob"
len(str)  # 字符串长度，结果为“6”
l = [1, 2, 3, 4, 5]
len(l)  # 列表元素个数，结果为“5”
```

`len`也可以用于计算文件的行数，以及目录下的文件个数。

### range

&emsp;&emsp;`range`可创建一个整数列表，一般用在`for`循环中：

``` python
range(start, stop [, step])
```

- `start`：计数从`start`开始，默认是从`0`开始。例如`range(5)`等价于`range(0, 5)`。
- `end`：计数到`end`结束，但不包括`end`。例如`range(0, 5)`的结果为`[0, 1, 2, 3, 4]`。
- `step`：这是步长，默认为`1`。例如`range(0, 5)`等价于`range(0, 5, 1)`。

``` python
>>> range(10)  # 从0开始到10
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> range(1, 11)  # 从1开始到11
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> range(0, 30, 5)  # 步长为5
[0, 5, 10, 15, 20, 25]
>>> range(0, 10, 3)  # 步长为3
[0, 3, 6, 9]
>>> range(0, -10, -1)  # 负数
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> range(0)
[]
>>> range(1, 0)
[]
```

以下是`range`在`for`中的使用，循环出`runoob`的每个字母：

``` python
x = 'runoob'

for i in range(len(x)):
    print(x[i])
```

&emsp;&emsp;`Python 3.x`的`range`函数返回的是一个整数序列的对象，而不是列表：

``` python
>>> type(range(10))
<class 'range'>
```

可以利用`list`函数返回列表：

``` python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### int

&emsp;&emsp;`int`用于将一个数字或字符串转换为整型：

``` python
class int(x, base=10)
```

参数`x`是字符串或数字，`base`是进制数。

``` python
>>> int()  # 不传入参数时，得到结果0
0
>>> int(3)
3
>>> int(3.6)
3
>>> int('12', 16)  # 如果带参数base，“12”要以字符串的形式进行输入
18
>>> int('0xa', 16)
10
>>> int('10', 8)
8
```

### abs

&emsp;&emsp;`abs`返回数字的绝对值：

``` python
abs(x)
```

参数`x`是数值表达式，可以是整数、浮点数和复数。如果参数是一个复数，则返回它的大小。

``` python
print("abs(-40):", abs(-40))  # 输出“abs(-40): 40”
print("abs(100.10):", abs(100.10))  # 输出“abs(100.10): 100.1”
```

### list

&emsp;&emsp;`list`用于将序列(列表、元组、字符串)转换为列表：

``` python
list(seq)
```

参数`seq`是要转换为列表的序列。

``` python
aTuple = (123, 'Google', 'Runoob', 'Taobao')
list1 = list(aTuple)
print(list1)  # 输出“[123, 'Google', 'Runoob', 'Taobao']”
str = "Hello World"
list2 = list(str)
print(list2)  # 输出“['H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd']”
```

### zip

&emsp;&emsp;`zip`用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用`*`操作符，可以将元组解压为列表。

``` python
zip([iterable, ...])
```

参数`iterable`是一个或多个迭代器。

``` python
>>> a = [1, 2, 3]
>>> b = [4, 5, 6]
>>> c = [4, 5, 6, 7, 8]
>>> zipped = zip(a, b)  # 打包为元组的列表
[(1, 4), (2, 5), (3, 6)]
>>> zip(a, c)  # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]
>>> zip(*zipped)  # 与zip相反，可理解为解压，返回二维矩阵式
[(1, 2, 3), (4, 5, 6)]
```

&emsp;&emsp;`zip(*iterables)`: Make an iterator that aggregates elements from each of the `iterables`.
&emsp;&emsp;Returns an iterator of tuples, where the `i-th` tuple contains the `i-th` element from each of the argument sequences or iterables. The iterator stops when the shortest input iterable is exhausted. With a single iterable argument, it returns an iterator of `1-tuples`. With no arguments, it returns an empty iterator. Equivalent to:

``` python
def zip(*iterables):
    # zip('ABCD', 'xy') --> Ax By
    sentinel = object()
    iterators = [iter(it) for it in iterables]

    while iterators:
        result = []

        for it in iterators:
            elem = next(it, sentinel)

            if elem is sentinel:
                return

            result.append(elem)

        yield tuple(result)
```

The `left-to-right` evaluation order of the iterables is guaranteed. This makes possible an idiom for clustering a data series into `n-length` groups using `zip(*[iter(s)] * n)`. This repeats the same iterator `n` times so that each output tuple has the result of `n` calls to the iterator. This has the effect of dividing the input into `n-length` chunks.
&emsp;&emsp;`zip()` should only be used with unequal length inputs when you don't care about trailing, unmatched values from the longer iterables. If those values are important, use `itertools.zip_longest()` instead.
&emsp;&emsp;`zip()` in conjunction with the `*` operator can be used to unzip a list:

``` python
>>> x = [1, 2, 3]
>>> y = [4, 5, 6]
>>> zipped = zip(x, y)
>>> list(zipped)
[(1, 4), (2, 5), (3, 6)]
>>> x2, y2 = zip(*zip(x, y))
>>> x == list(x2) and y == list(y2)
True
```

### sorted

&emsp;&emsp;`sorted`函数对所有可迭代的对象进行排序操作。`sort`与`sorted`区别：`sort`是应用在`list`上的方法，`sorted`可以对所有可迭代的对象进行排序操作。`list`的`sort`方法返回的是对已经存在的列表进行操作，而内建函数`sorted`方法返回的是一个新的`list`，而不是在原来的基础上进行的操作。

``` python
sorted(iterable [, cmp [, key [, reverse]]])
```

- `iterable`：可迭代对象。
- `cmp`：用于比较的函数，这个具有两个参数，参数的值都是从可迭代对象中取出。此函数必须遵守的规则为：大于则返回`1`，小于则返回`-1`，等于则返回`0`。
- `key`：主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
- `reverse`：排序规则，为`True`表示降序，为`False`表示升序(默认)。

``` python
>>> a = [5, 7, 6, 3, 4, 1, 2]
>>> b = sorted(a)  # 保留原列表
>>> a
[5, 7, 6, 3, 4, 1, 2]
>>> b
[1, 2, 3, 4, 5, 6, 7]
>>> L = [('b',2),('a',1),('c',3),('d',4)]
>>> sorted(L, cmp=lambda x,y:cmp(x[1],y[1]))  # 利用cmp函数
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
>>> sorted(L, key=lambda x:x[1])  # 利用key
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
>>> students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
>>> sorted(students, key=lambda s: s[2])  # 按年龄排序
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
>>> sorted(students, key=lambda s: s[2], reverse=True)  # 按降序
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
```

按年龄进行排序可以使用如下方法：

``` python
>>> from operator import itemgetter
>>> students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
>>> sorted(students, key=itemgetter(2))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
```

### round

&emsp;&emsp;`round`返回浮点数`x`的四舍五入值：

``` python
round(x [, n])
```

参数`x`是一个浮点数，`n`是保留的小数位数(可选项，如果不写的话，默认保留到整数)。

``` python
print("round(70.23456):", round(70.23456))
print("round(56.659, 1):", round(56.659, 1))
print("round(80.264, 2):", round(80.264, 2))
print("round(100.000056, 3):", round(100.000056, 3))
print("round(-100.000056, 3):", round(-100.000056, 3))
```

执行结果：

``` python
round(70.23456): 70
round(56.659, 1): 56.7
round(80.264, 2): 80.26
round(100.000056, 3): 100.0
round(-100.000056, 3): -100.0
```

在实际使用中发现，`round`函数并不总是如上所说的四舍五入：

``` pythonb
round(2.355, 2)  # 执行结果是2.35
```

而且在不用的`python`版本中，其结果也不一样。
&emsp;&emsp;在`python 2.7`的`doc`中，`round`的说明写着`Values are rounded to the closest multiple of 10 to the power minus ndigits; if two multiples are equally close, rounding is done away from 0.`，保留值将保留到离上一位更近的一端(四舍六入)，如果距离两端一样远，则保留到离`0`远的一边。所以`round(0.5)`会近似到`1`，而`round(-0.5)`会近似到`-1`。但是到了`python 3.5`的`doc`中，文档变成了`values are rounded to the closest multiple of 10 to the power minus ndigits; if two multiples are equally close, rounding is done toward the even choice.`，如果距离两边一样远，会保留到偶数的一边。比如`round(0.5)`和`round(-0.5)`都会保留到`0`，而`round(1.5)`会保留到`2`。所以如果有项目是从`py2`迁移到`py3`的，需要注意`round`函数。

### map

&emsp;&emsp;`map`会根据提供的函数对指定序列做映射。参数`function`以参数序列`iterable`中的每一个元素调用`function`函数。返回包含每次`function`函数返回值的新列表。

``` python
map(function, iterable, ...)
```

实例如下：

``` python
def square(x):  # 计算平方数
    return x ** 2

result = map(square, [1, 2, 3, 4, 5])
print(list(result))  # 执行结果是[1, 4, 9, 16, 25]
result = map(lambda x: x ** 2, [1, 2, 3, 4, 5])
print(list(result))  # 执行结果是[1, 4, 9, 16, 25]
result = map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
print(list(result))  # 执行结果是[3, 7, 11, 15, 19]
```

&emsp;&emsp;`map`还可以用于转换列表中的元素类型：

``` python
>>> list_float = [1.3, 2.3, 4, 5]
>>> list_int = map(int, list_float)
>>> print(list(list_int))
[1, 2, 4, 5]
```

### str

&emsp;&emsp;`str`将对象转化为`string`格式。

``` python
class str(object='')
```

参数`object`是对象。

``` python
>>> s = 'RUNOOB'
>>> str(s)
'RUNOOB'
>>> dict = {'runoob': 'runoob.com', 'google': 'google.com'}
>>> str(dict)
"{'runoob': 'runoob.com', 'google': 'google.com'}"
```

### filter

&emsp;&emsp;`filter`函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。参数`function`为函数，参数`iterable`为序列，序列的每个元素作为参数传递给函数进行判，然后返回`True`或`False`，最后将返回`True`的元素放到新列表中。

``` python
filter(function, iterable)
```

实例如下：

``` python
def is_odd(n):
    return n % 2 == 1

newlist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(list(newlist))  # 输出“[1, 3, 5, 7, 9]”
```

### bool

&emsp;&emsp;`bool`函数用于将给定参数转换为布尔类型，如果没有参数，则返回`False`。`bool`是`int`的子类。

``` python
class bool([x])
```

参数`x`是要进行转换的参数。

``` python
>>> bool()
False
>>> bool(0)
False
>>> bool(1)
True
>>> bool(2)
True
>>> issubclass(bool, int)  # bool是int的子类
True
```

### super

&emsp;&emsp;`super`用于调用一个父类(超类)，并返回该父类实例。

``` python
super(type[, object-or-type])
```

参数`type`是类，`object-or-type`一般是`self`，该方法没有返回值。

``` python
class FooParent(object):
    def __init__(self):
        self.parent = 'I\'m the parent.'
        print('Parent')

    def bar(self, message):
        print("%s from Parent" % message)

class FooChild(FooParent):
    def __init__(self):
        # super(FooChild, self)首先找到FooChild的父类(就是类FooParent)，
        # 然后把类B的对象FooChild转换为类FooParent的对象
        super(FooChild, self).__init__()
        print('Child')

    def bar(self, message):
        super(FooChild, self).bar(message)
        print('Child bar fuction')
        print(self.parent)

if __name__ == '__main__':
    fooChild = FooChild()
    fooChild.bar('HelloWorld')
```

执行结果：

``` python
Parent
Child
HelloWorld from Parent
Child bar fuction
I'm the parent.
```

### min

&emsp;&emsp;`min`返回给定参数的最小值，参数可以为序列，而`max`方法返回的是最大值。

``` python
min(x, y, z, ...)
```

参数`x`、`y`和`z`都是数值表达式。

``` python
print("min(80, 100, 1000):", min(80, 100, 1000))
print("min(-20, 100, 400):", min(-20, 100, 400))
print("min(-80, -20, -10):", min(-80, -20, -10))
print("min(0, 100, -400):", min(0, 100, -400))
```

执行结果：

``` python
min(80, 100, 1000): 80
min(-20, 100, 400): -20
min(-80, -20, -10): -80
min(0, 100, -400): -400
```

### sum

&emsp;&emsp;`sum`对序列进行求和计算：

``` python
sum(iterable[, start])
```

参数`iterable`是可迭代对象，例如列表；`start`是指定相加的参数，如果没有设置这个值，默认为`0`。该方法返回计算结果。

``` python
>>> sum([0, 1, 2])
3
>>> sum((2, 3, 4), 1)  # 元组计算总和后再加1
10
>>> sum([0, 1, 2, 3, 4], 2)  # 列表计算总和后再加2
12
```

### isinstance

&emsp;&emsp;`isinstance`函数来判断一个对象是否是一个已知的类型，类似于`type`。`isinstance`与`type`的区别如下：

- `type`不会认为子类是一种父类类型，不考虑继承关系。
- `isinstance`会认为子类是一种父类类型，考虑继承关系。

如果要判断两个类型是否相同，推荐使用`isinstance`。

``` python
isinstance(object, classinfo)
```

参数`object`是实例对象；`classinfo`可以是直接或间接类名、基本类型或者由它们组成的元组。如果对象的类型与参数`classinfo`相同，则返回`True`，否则返回`False`。

``` python
>>> a = 2
>>> isinstance (a, int)
True
>>> isinstance (a, str)
False
>>> isinstance (a, (str, int, list))  # 是元组中的一个，返回True
True
```

`type`与`isinstance`的区别如下：

``` python
class A:
    pass

class B(A):
    pass

isinstance(A(), A)  # returns True
type(A()) == A      # returns True
isinstance(B(), A)  # returns True
type(B()) == A      # returns False
```

&emsp;&emsp;对于基本类型来说，`classinfo`可以是`int`、`float`、`bool`、`complex`、`str`、`list`、`dict`、`set`和`tuple`。

### dir

&emsp;&emsp;`dir`不带参数时，返回当前范围内的变量、方法和定义的类型列表；带参数时，返回参数的属性、方法列表。如果参数包含方法`__dir__`，该方法将被调用；如果参数不包含`__dir__`，该方法将最大限度地收集参数信息。

``` python
dir([object])
```

参数`object`是对象、变量或类型。

``` python
>>> dir()  # 获得当前模块的属性列表
['__builtins__', '__doc__', '__name__', '__package__', 'arr', 'myslice']
>>> dir([ ])  # 查看列表的方法
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__delslice__',
'__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getslice__',
'__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__iter__', '__le__', '__len__', '__lt__',
'__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__',
'__rmul__', '__setattr__', '__setitem__', '__setslice__', '__sizeof__', '__str__', '__subclasshook__',
'append', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

### set

&emsp;&emsp;`set`创建一个无序不重复元素集，可进行关系测试、删除重复数据，还可以计算交集、差集、并集等。

``` python
class set([iterable])
```

参数`iterable`是可迭代对象。

``` python
>>> x = set('runoob')
>>> y = set('google')
>>> x, y
(set(['b', 'r', 'u', 'o', 'n']), set(['e', 'o', 'g', 'l']))  # 重复的元素被删除
>>> x & y  # 交集
set(['o'])
>>> x | y  # 并集
set(['b', 'e', 'g', 'l', 'o', 'n', 'r', 'u'])
>>> x - y  # 差集
set(['r', 'b', 'u', 'n'])
```

### iter

&emsp;&emsp;`iter`用来生成迭代器。

``` python
iter(object[, sentinel])
```

参数`object`是支持迭代的集合对象；对于`sentinel`，如果传递了第二个参数，则参数`object`必须是一个可调用的对象(例如函数)，此时`iter`创建了一个迭代器对象，每次调用这个迭代器对象的`__next__`方法时，都会调用`object`。该函数返回迭代器对象。

``` python
>>> lst = [1, 2, 3]
>>> for i in iter(lst):
...     print(i)
...
1
2
3
```

### next

&emsp;&emsp;`next`返回迭代器的下一个项目。

``` python
next(iterator [, default])
```

参数`iterator`是可迭代对象；`default`是可选的，用于设置在没有下一个元素时返回的默认值，如果不设置，又没有下一个元素时则会触发`StopIteration`异常。

``` python
it = iter([1, 2, 3, 4, 5])  # 首先获得Iterator对象

while True:  # 循环：
    try:
        x = next(it)  # 获得下一个值
        print(x)
    except StopIteration:  # 遇到StopIteration就退出循环
        break
```

### eval

&emsp;&emsp;`eval`函数用来执行一个字符串表达式，并返回表达式的值。

``` python
eval(expression[, globals[, locals]])
```

参数`expression`是表达式；`globals`是变量作用域，全局命名空间，如果被提供，则必须是一个字典对象；`locals`是变量作用域，局部命名空间，如果被提供，可以是任何映射对象。

``` python
>>> x = 7
>>> eval('3 * x')
21
>>> eval('pow(2,2)')
4
>>> eval('2 + 2')
4
>>> n = 81
>>> eval("n + 4")
85
```

&emsp;&emsp;`eval`在做计算前并不知道需要转化的内容是不是合法的(安全的)`python`数据类型，只是在调用函数的时候去计算。如果被计算的内容不是合法的，`python`类型就会抛出异常。
&emsp;&emsp;`ast.literal`则会判断需要计算的内容计算后是不是合法的`python`类型，如果是则进行运算，否则就不进行运算。

### staticmethod

&emsp;&emsp;`staticmethod`返回函数的静态方法。该方法不强制要求传递参数：

``` python
class C(object):
    @staticmethod
    def f(arg1, arg2, ...):
        ...
```

以上实例声明了静态方法`f`，类可以不用实例化就可以调用该方法(如`C.f()`)，当然也可以实例化后调用(如`C().f()`)。

``` python
class C(object):
    @staticmethod
    def f():
        print('runoob')

C.f()  # 静态方法无需实例化
cobj = C()
cobj.f()  # 也可以实例化后调用
```

### bytearray

&emsp;&emsp;`bytearray`返回一个字节数组。这个数组里的元素是可变的，并且每个元素的值范围是`0 <= x < 256`。

``` python
class bytearray([source[, encoding[, errors]]])
```

- 如果`source`为整数，则返回一个长度为`source`的初始化数组。
- 如果`source`为字符串，则按照指定的`encoding`将字符串转换为字节序列。
- 如果`source`为可迭代类型，则元素必须为`[0, 255]`中的整数。
- 如果没有输入任何参数，默认就是初始化数组为`0`个元素。

``` python
>>> bytearray()
bytearray(b'')
>>> bytearray([1, 2, 3])
bytearray(b'\x01\x02\x03')
>>> bytearray('runoob', 'utf-8')
bytearray(b'runoob')
```

### repr

&emsp;&emsp;`repr`将对象转化为供解释器读取的形式。

``` python
repr(object)
```

该函数返回一个对象的`string`格式。

``` python
>>> s = 'RUNOOB'
>>> repr(s)
"'RUNOOB'"
>>> dict = {'runoob': 'runoob.com', 'google': 'google.com'};
>>> repr(dict)
"{'google': 'google.com', 'runoob': 'runoob.com'}"
```

### enumerate

&emsp;&emsp;`enumerate`用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在`for`循环当中。

``` python
enumerate(sequence [, start=0])
```

参数`sequence`是一个序列、迭代器或其他支持迭代对象，`start`是下标起始位置，该函数返回`enumerate`对象。

``` python
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))  # 下标从“1”开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

### locals

&emsp;&emsp;`locals`函数会以字典类型返回当前位置的全部局部变量。对于函数、方法、`lambda`函式、类、以及实现了`__call__`方法的类实例，它都返回`True`。

``` python
>>> def runoob(arg):  # 两个局部变量，即arg和z
...     z = 1
...     print (locals())
...
>>> runoob(4)
{'z': 1, 'arg': 4}  # 返回一个名字/值对的字典
```

### var

&emsp;&emsp;`vars`返回对象`object`的属性和属性值的字典对象。

``` python
vars([object])
```

参数`object`是对象。如果没有参数，就打印当前调用位置的属性和属性值，类似于`locals`。

``` python
>>> print(vars())
{
    '__builtins__': <module '__builtin__' (built-in)>,
    '__name__': '__main__', '__doc__': None, '__package__': None
}
>>> class Runoob:
...     a = 1
...
>>> print(vars(Runoob))
{'a': 1, '__module__': '__main__', '__doc__': None}
>>> runoob = Runoob()
>>> print(vars(runoob))
{}
```

### exec

&emsp;&emsp;`exec`执行储存在字符串或文件中的`Python`语句，相比于`eval`，`exec`可以执行更复杂的`Python`代码。

``` python
exec(object[, globals[, locals]])
```

- `object`：必选参数，表示需要被指定的`Python`代码，它必须是字符串或`code`对象。如果`object`是一个字符串，该字符串会先被解析为一组`Python`语句，然后再执行(除非发生语法错误)。如果`object`是一个`code`对象，那么它只是被简单的执行。
- `globals`：可选参数，表示全局命名空间(存放全局变量)。如果被提供，则必须是一个字典对象。
- `locals`：可选参数，表示当前局部命名空间(存放局部变量)。如果被提供，可以是任何映射对象。如果该参数被忽略，那么它将会取与`globals`相同的值。

`exec`返回值永远为`None`。单行语句字符串如下：

``` python
>>> exec('print("Hello World")')
Hello World
>>> exec("print('runoob.com')")
runoob.com
```

多行语句字符串如下：

``` python
x = 10

expr = """
z = 30
sum = x + y + z
print(sum)
"""

def func():
    y = 20
    exec(expr)  # 输出“60”
    exec(expr, {'x': 1, 'y': 2})  # 输出“33”
    exec(expr, {'x': 1, 'y': 2}, {'y': 3, 'z': 4})  # 输出“34”

func()
```