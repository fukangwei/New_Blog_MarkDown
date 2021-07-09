---
title: Python内置函数
categories: Python语法
date: 2019-02-24 10:42:11
---
### chr

&emsp;&emsp;`chr`用一个范围在`range(256)`内的整数作为参数，返回一个对应的字符：<!--more-->

``` python
chr(i)
```

参数`i`可以是`10`进制，也可以是`16`进制形式的数字。

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

&emsp;&emsp;`ord`以一个字符作为参数，返回对应的`ASCII`码：

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

&emsp;&emsp;`len`返回对象的长度或项目个数：

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

&emsp;&emsp;`len`也可以用于计算文件的行数，以及目录下的文件个数。

### range

&emsp;&emsp;`range`用于创建一个整数列表：

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

&emsp;&emsp;以下是`range`在`for`中的使用，循环出`runoob`的每个字母：

``` python
x = 'runoob'

for i in range(len(x)):
    print(x[i])
```

&emsp;&emsp;可以利用`list`函数返回列表：

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

&emsp;&emsp;`list`用于将序列转换为列表：

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

&emsp;&emsp;`zip`用于将可迭代对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。
&emsp;&emsp;如果可迭代对象的元素个数不一致，则返回的列表长度与最短的对象相同。
&emsp;&emsp;利用`*`操作符，可以将元组解压为列表。

``` python
zip([iterable, ...])
```

参数`iterable`是一个或多个可迭代对象。

``` python
>>> a = [1, 2, 3]
>>> b = [4, 5, 6]
>>> c = [4, 5, 6, 7, 8]
>>> zipped = zip(a, b)  # 打包为元组的列表
[(1, 4), (2, 5), (3, 6)]
>>> zip(a, c)  # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]
>>> zip(*zipped)  # 与zip相反，可理解为解压
[(1, 2, 3), (4, 5, 6)]
```

### sorted

&emsp;&emsp;`sorted`函数对所有可迭代对象进行排序：

``` python
sorted(iterable[, cmp[, key[, reverse]]])
```

- `iterable`：可迭代对象。
- `cmp`：用于比较的函数，这个函数具有`2`个参数。此函数必须遵守的规则：大于则返回`1`，小于则返回`-1`，等于则返回`0`。
- `key`：用来进行比较的元素，只有一个参数，指定可迭代对象中的一个元素来进行排序。
- `reverse`：排序规则，为`True`表示降序，为`False`表示升序(默认)。

``` python
>>> a = [5, 7, 6, 3, 4, 1, 2]
>>> b = sorted(a)  # 保留原列表
>>> a
[5, 7, 6, 3, 4, 1, 2]
>>> b
[1, 2, 3, 4, 5, 6, 7]
>>> L = [('b', 2), ('a', 1), ('c', 3),('d', 4)]
>>> sorted(L, cmp=lambda x, y : cmp(x[1], y[1]))  # 利用cmp函数
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
>>> sorted(L, key=lambda x : x[1])  # 利用key
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
>>> students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
>>> sorted(students, key=lambda s : s[2])  # 按年龄排序
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
>>> sorted(students, key=lambda s : s[2], reverse=True)  # 按降序
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
```

&emsp;&emsp;按年龄进行排序可以使用如下方法：

``` python
>>> from operator import itemgetter
>>> students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
>>> sorted(students, key=itemgetter(2))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
```

### round

&emsp;&emsp;`round`返回浮点数`x`的四舍五入值：

``` python
round(x[, n])
```

- 参数`x`是一个浮点数。
- 参数`n`是保留的小数位数，默认保留到整数。

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

### map

&emsp;&emsp;`map`会根据提供的函数对指定序列做出映射：

``` python
map(function, iterable, ...)
```

`map`将对序列`iterable`中的每一个元素调用函数`function`。

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

&emsp;&emsp;`str`将对象转化为`string`格式：

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

&emsp;&emsp;`filter`函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表：

``` python
filter(function, iterable)
```

- `function`为函数。
- `iterable`为序列。

``` python
def is_odd(n):
    return n % 2 == 1

newlist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(list(newlist))  # 输出“[1, 3, 5, 7, 9]”
```

### bool

&emsp;&emsp;`bool`函数用于将给定参数转换为布尔类型：

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

&emsp;&emsp;`super`用于调用一个父类，并返回该父类实例：

``` python
super(type[, object-or-type])
```

参数`type`是类，`object-or-type`一般是`self`。

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

### min和max

&emsp;&emsp;`min`返回给定参数的最小值，而`max`方法返回的是最大值。

``` python
min(x, y, z, ...)
max(x, y, z, ...)
```

参数`x`、`y`和`z`都是数值表达式。

``` python
print("min(80, 100, 1000):", min(80, 100, 1000))
print("min(-20, 100, 400):", min(-20, 100, 400))
print("max(-80, -20, -10):", max(-80, -20, -10))
print("max(0, 100, -400):", max(0, 100, -400))
```

执行结果：

``` python
min(80, 100, 1000): 80
min(-20, 100, 400): -20
max(-80, -20, -10): -10
max(0, 100, -400): 100
```

### sum

&emsp;&emsp;`sum`对序列进行求和计算：

``` python
sum(iterable[, start])
```

- 参数`iterable`是可迭代对象，例如列表。
- 参数`start`是指定相加的参数，默认为`0`。

``` python
>>> sum([0, 1, 2])
3
>>> sum((2, 3, 4), 1)  # 元组计算总和后再加1
10
>>> sum([0, 1, 2, 3, 4], 2)  # 列表计算总和后再加2
12
```

### isinstance

&emsp;&emsp;`isinstance`函数来判断一个对象是否为一个已知的类型，类似于`type`。

``` python
isinstance(object, classinfo)
```

- 参数`object`是实例对象。
- 参数`classinfo`可以是直接或间接类名、基本类型或者由它们组成的元组。

如果对象的类型与参数`classinfo`相同，则返回`True`，否则返回`False`。

``` python
>>> a = 2
>>> isinstance (a, int)
True
>>> isinstance (a, str)
False
>>> isinstance (a, (str, int, list))  # 是元组中的一个，返回True
True
```

&emsp;&emsp;`isinstance`与`type`的区别如下：

- `type`不会认为子类是一种父类类型，不考虑继承关系。
- `isinstance`会认为子类是一种父类类型，考虑继承关系。

如果要判断两个类型是否相同，推荐使用`isinstance`。

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

&emsp;&emsp;`iter`用来生成迭代器：

``` python
iter(object)
```

参数`object`是支持迭代的集合对象。

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

&emsp;&emsp;`next`返回迭代器的下一个项目：

``` python
next(iterator [, default])
```

- 参数`iterator`是可迭代对象。
- 参数`default`是可选的：

1. 用于设置在没有下一个元素时，返回的默认值。
2. 如果不设置，又没有下一个元素时，则会触发`StopIteration`。

``` python
it = iter([1, 2, 3, 4, 5])

while True:
    try:
        x = next(it)  # 获得下一个值
        print(x)
    except StopIteration:  # 遇到StopIteration时，就退出循环
        break
```

### eval

&emsp;&emsp;`eval`函数用来执行一个字符串表达式，并返回表达式的值：

``` python
eval(expression)
```

参数`expression`是表达式。

``` python
>>> x = 7
>>> eval('3 * x')
21
>>> eval('pow(2, 2)')
4
>>> eval('2 + 2')
4
>>> n = 81
>>> eval("n + 4")
85
```

### staticmethod

&emsp;&emsp;`staticmethod`返回函数的静态方法：

``` python
class C(object):
    @staticmethod
    def f(arg1, arg2, ...):
        ...
```

以上实例声明了静态方法`f`：

1. 类可以不用实例化就可以调用该方法，如`C.f()`。
2. 当然也可以实例化后调用，如`C().f()`。

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

&emsp;&emsp;`bytearray`返回一个字节数组，该数组里元素的值范围是`0 <= x < 256`：

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

&emsp;&emsp;`repr`将对象转化为供解释器读取的形式：

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

&emsp;&emsp;`enumerate`用于将一个可遍历的数据对象组合为一个索引序列，同时列出数据和数据下标：

``` python
enumerate(sequence[, start=0])
```

- 参数`sequence`是一个序列、迭代器或其他支持迭代的对象。
- 参数`start`是下标起始位置。

``` python
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))  # 下标从“1”开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

### locals

&emsp;&emsp;`locals`函数会以字典类型返回当前位置的全部局部变量：

``` python
>>> def runoob(arg):  # 两个局部变量，即arg和z
...     z = 1
...     print (locals())
...
>>> runoob(4)
{'z': 1, 'arg': 4}  # 返回一个名字/值对的字典
```

### var

&emsp;&emsp;`vars`返回对象`object`的属性和属性值的字典对象：

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

&emsp;&emsp;`exec`执行储存在字符串或文件中的`Python`语句。相比于`eval`，`exec`可以执行更复杂的代码：

``` python
exec(object[, globals[, locals]])
```

- `object`：需要被执行的`Python`代码，必须是字符串或`code`对象。

1. 如果`object`是一个字符串，该字符串会先被解析为一组`Python`语句，然后再执行，除非发生语法错误。
2. 如果`object`是一个`code`对象，那么它只是被简单地执行。

- `globals`：可选参数，表示全局命名空间。如果被提供，则必须是一个字典对象。
- `locals`：可选参数，表示当前局部命名空间。

1. 如果被提供，可以是任何映射对象。
2. 如果该参数被忽略，那么它将会取与`globals`相同的值。

&emsp;&emsp;单行语句字符串如下：

``` python
>>> exec('print("Hello World")')
Hello World
>>> exec("print('runoob.com')")
runoob.com
```

&emsp;&emsp;多行语句字符串如下：

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

### id

&emsp;&emsp;`id`函数用于获取对象的内存地址：

``` python
>>> a = 'runoob'
>>> id(a)
2434848042264
```