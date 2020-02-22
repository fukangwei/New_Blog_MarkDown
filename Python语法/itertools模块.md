---
title: itertools模块
categories: Python语法
date: 2019-02-10 20:31:27
---
&emsp;&emsp;`Python`提供了一个非常棒的模块用于创建自定义的迭代器，这个模块就是`itertools`。`itertools`提供的工具相当高效且节省内存，使用这些工具，你将能够创建自己定制的迭代器用于高效率的循环。<!--more-->

### 无限迭代器

&emsp;&emsp;`itertools`包自带了三个可以无限迭代的迭代器，这意味着当你使用它们时，需要知道到底是最终会停止迭代器，还是需要无限地迭代下去。这些无限迭代器在生成数字或者在长度未知的可迭代对象(`iterables`)中循环时相当有用。

#### count(初值=0, 步长=1)

&emsp;&emsp;`count`迭代器会返回从传入的起始参数开始的均匀间隔的数值。`count`也可以接收指定的步长参数。

``` python
from itertools import count

for i in count(18):
    if i > 20:
        break
    else:
        print(i)
```

执行结果：

``` python
18
19
20
```

&emsp;&emsp;另一种控制无限迭代器输出的方式是使用`itertools`的子模块`islice`。

``` python
from itertools import islice, count

for i in islice(count(10), 5):
    print(i)
```

执行结果：

``` python
10
11
12
13
14
```

`islice`的第二个参数控制何时停止迭代，其含义并不是`达到数字5时停止`，而是`当迭代了5次之后停止`。

#### cycle(可迭代对象)

&emsp;&emsp;`itertools`中的`cycle`迭代器允许你创建一个能在一组值间无限循环的迭代器。

``` python
from itertools import cycle

count = 0
for item in cycle('XYZ'):
    if count > 4:
        break

    print(item)
    count += 1
```

执行结果：

``` python
X
Y
Z
X
Y
```

&emsp;&emsp;你也可以用`Python`内建的`next`函数对`itertools`创建的迭代器进行循环：

``` python
>>> polys = ['triangle', 'square', 'pentagon', 'rectangle']
>>> iterator = cycle(polys)
>>> next(iterator)
'triangle'
>>> next(iterator)
'square'
>>> next(iterator)
'pentagon'
>>> next(iterator)
'rectangle'
>>> next(iterator)
'triangle'
>>> next(iterator)
'square'
```

上面的代码中，我们创建一个简单的多边形组成的列表，然后传入`cycle`。我们用一个变量存储新建的迭代器，然后将这个变量传入`next`函数。每一次调用`next`，都将返回迭代器中的下一个值。由于迭代器是无限的，我们可以一直调用`next`而永远不会到尽头。

#### repeat(对象[, 次数])

&emsp;&emsp;`repeat`迭代器会一遍遍地返回传入的对象直至永远，除非你设定了`times`参数。这与`cycle`非常相像，除了`repeat`不会在多个值之间循环。

``` python
>>> from itertools import repeat
>>> repeat(5, 5)
repeat(5, 5)
>>> iterator = repeat(5, 5)
>>> next(iterator)
5
>>> next(iterator)
5
>>> next(iterator)
5
>>> next(iterator)
5
>>> next(iterator)
5
>>> next(iterator)
Traceback (most recent call last):
  Python Shell, prompt 21, line 1
builtins.StopIteration:
```

先导入`repeat`，然后设定其重复五次。接着连续六次调用`next`函数，观察它是否工作正常。当运行这段代码，将引发`StopIteration`，因为最后一次调用`next`函数时，迭代器中的值已经全部返回了。

### 可终止的迭代器

&emsp;&emsp;多数用`itertools`创建的迭代器并不是无限的。为了对于输出的可读的结果，我们将使用`Python`内建列表存储。如果不使用列表，你将只能打印出`itertools`对象。

#### accumulate(可迭代对象 [, 函数])

&emsp;&emsp;`accumulate`迭代器将返回累计求和结果，或者传入两个参数的话，由传入的函数累积计算的结果。默认设定为相加。

``` python
>>> from itertools import accumulate
>>> list(accumulate(range(10)))
[0, 1, 3, 6, 10, 15, 21, 28, 36, 45]
```

导入了`accumulate`，然后传入`10`个数字(`0`至`9`)。迭代器将传入数字依次累加，所以第一个是`0`，第二个是`0 + 1`，第三个是`1 + 2`，以此类推。现在导入`operator`模块，然后添加进去：

``` python
>>> import operator
>>> list(accumulate(range(1, 5), operator.mul))
[1, 2, 6, 24]
```

这里传入了数字`1`至`4`到`accumulate`迭代器中，还传入了一个函数`operator.mul`，这个函数将接收的参数相乘。

#### chain(*可迭代对象)

&emsp;&emsp;`chain`迭代器能够将多个可迭代对象合并成一个更长的可迭代对象。

``` python
from itertools import chain

cmd = ['ls', '/some/dir']
numbers = list(range(5))
my_list = list(chain(['foo', 'bar'], cmd, numbers))
print(my_list)
```

执行结果：

``` python
['foo', 'bar', 'ls', '/some/dir', 0, 1, 2, 3, 4]
```

实际上不使用`itertools`，也有其他方法能够实现这一要求：

``` python
>>> my_list = ['foo', 'bar']
>>> my_list += cmd + numbers
>>> my_list
['foo', 'bar', 'ls', '/some/dir', 0, 1, 2, 3, 4]
```

#### chain.from_iterable(可迭代对象)

&emsp;&emsp;你也可以用`chain`的一个方法，叫做`from_iterable`，这个方法与直接用`chain`有些细微的差别。不同于直接传入一系列可迭代对象，你必须传入一个嵌套的列表。

``` python
>>> from itertools import chain
>>> numbers = list(range(5))
>>> cmd = ['ls', '/some/dir']
>>> chain.from_iterable(cmd, numbers)
Traceback (most recent call last):
  Python Shell, prompt 66, line 1
builtins.TypeError: from_iterable() takes exactly one argument (2 given)
>>> list(chain.from_iterable([cmd, numbers]))
['ls', '/some/dir', 0, 1, 2, 3, 4]
```

我们尝试传入两个列表，但却出现了`TypeError`。为了修正这个错误，我们将`cmd`和`numbers`放入一个列表中，将这个嵌套的列表传入`from_iterable`。

#### compress(数据, 选择器)

&emsp;&emsp;子模块`compress`在用一个可迭代对象过滤另一个可迭代对象时十分有用，这是通过将作为选择器的可迭代对象取为布尔值列表来实现的。

``` python
>>> from itertools import compress
>>> letters = 'ABCDEFG'
>>> bools = [True, False, True, True, False]
>>> list(compress(letters, bools))
['A', 'C', 'D']
```

`compress`函数将交替查看两个可迭代对象。先检查第一个可迭代对象，然后是第二个，如果第二个的元素为`True`，则保留第一个对应元素；如果为`False`，则丢弃第一个对应元素。

#### dropwhile(断言, 可迭代对象)

&emsp;&emsp;`itertools`还提供了迭代器`dropwhile`，只要过滤器判定是`True`，`dropwhile`迭代器就会排除这些元素。因此，在出现断言为`False`之前，你不会看到任何输出结果。这可能导致启动时间非常长，这点应当注意。

``` python
>>> from itertools import dropwhile
>>> list(dropwhile(lambda x: x < 5, [1,4,6,4,1]))
[6, 4, 1]
```

先导入`dropwhile`，然后传入一个简单的`lambda`声明的函数：如果`x`小于`5`，`lambda`将返回`True`，否则返回`False`。`dropwhile`将遍历整个列表，然后将每个元素传入`lambda`。如果`lambda`返回`True`，则舍弃该值。一旦遇到了数字`6`，`lambda`将返回`False`，因此将保留数字`6`以及之后余下的元素。
&emsp;&emsp;当然也可以采用一般的函数：

``` python
from itertools import dropwhile

def greater_than_five(x):
    return x > 5

my_list = list(dropwhile(greater_than_five, [6, 7, 8, 9, 1, 2, 3, 10]))
print(my_list)
```

执行结果：

``` python
[1, 2, 3, 10]
```

#### filterfalse(断言, 可迭代对象)

&emsp;&emsp;`itertools`中的`filterfalse`函数与`dropwhile`非常类似，只是`filterfalse`返回断言为`False`的那些值，而不是舍弃断言为`True`的值。

``` python
from itertools import filterfalse

def greater_than_five(x):
    return x > 5

my_list = list(filterfalse(greater_than_five, [6, 7, 8, 9, 1, 2, 3, 10]))
print(my_list)
```

执行结果：

``` python
[1, 2, 3]
```

如果整数值小于`5`，这个整数将保留下来，否则将被舍弃。注意到，这里的结果仅有`1`、`2`和`3`。与`dropwhile`不同，`filterfalse`将对全部的值进行条件判断。

#### groupby(可迭代对象, 键=None)

&emsp;&emsp;将`key`函数作用于原循环器的各个元素，根据`key`函数结果，将拥有相同函数结果的元素分到一个新的循环器。每个新的循环器以函数返回结果为标签。
&emsp;&emsp;这就好像一群人的身高作为循环器，我们可以使用这样一个`key`函数：如果身高大于`180`，返回`tall`；如果身高低于`160`，返回`short`；中间的返回`middle`。最终，所有身高将分为三个循环器，即`tall`、`short`、`middle`：

``` python
from itertools import *

def height_class(h):
    if h > 180:
        return 'tall'
    elif h < 160:
        return 'short'
    else:
        return 'middle'

friends = [191, 158, 159, 165, 170, 177, 181, 182, 190]
friends = sorted(friends, key=height_class)

for m, n in groupby(friends, key=height_class):
    print(m, list(n))
    print("---------")
```

执行结果：

``` python
middle [165, 170, 177]
---------
short [158, 159]
---------
tall [191, 181, 182, 190]
---------
```

`groupby`的功能类似于`UNIX`中的`uniq`命令。分组之前需要使用`sorted`对原循环器的元素，根据`key`函数进行排序，让同组元素先在位置上靠拢。

#### islice(可迭代变量, 起始值, 终止值 [, 步长])

&emsp;&emsp;`islice`是一个返回可迭代对象选定元素的迭代器。简而言之，`islice`所做的是利用可迭代对象的索引实现切片，然后以迭代器的形式返回所选元素。实际上`islice`有两种实现方式，一种是`itertools.islice(iterable, stop)`，还有一种更符合`Python`惯例的形式：`islice(iterable, start, stop[, step])`。来看看第一种形式：

``` python
>>> from itertools import islice
>>> iterator = islice('123456', 4)
>>> next(iterator)
'1'
>>> next(iterator)
'2'
>>> next(iterator)
'3'
>>> next(iterator)
'4'
>>> next(iterator)
Traceback (most recent call last):
  Python Shell, prompt 15, line 1
builtins.StopIteration:
```

在以上的代码中，我们传入了一个`6`个字符组成的字符串以及终止参数`4`给`isilce`。这表示`islice`返回的迭代器将包含传入字符串的前`4`个字符。为了验证，我们连续四次调用`next`函数。`Python`能够智能地识别是否只有`2`个传入参数，若是，则第二个参数就取为终止参数。
&emsp;&emsp;我们再试试传入三个参数来验证是否可以同时传入起始值和终止值。`count`工具可以帮助我们解释这一概念：

``` python
from itertools import islice
from itertools import count

for i in islice(count(), 3, 7):
    print(i)
```

执行结果：

``` python
3
4
5
6
```

这里我们调用了`count`，并指定`islice`从`3`开始，到`7`时结束。这和使用`slice`效果是一样的，不同之处在于传入的是迭代器，返回的是新的迭代器。

#### starmap(函数, 可迭代对象)

&emsp;&emsp;`starmap`工具能够创建一个用传入的函数和可迭代对象计算的迭代器。如文档中所言，`map`和`starmap`的区别正如`function(a, b)`和`function(*c)`的区别。

``` python
from itertools import starmap

def add(a, b):
    return a + b

for item in starmap(add, [(2, 3), (4, 5)]):
    print(item)
```

执行结果：

``` python
5
9
```

这里先创建了一个接受两个参数的求和函数，接下来创建了一个`for`循环，并调用`starmap`函数。`starmap`函数的第一个传入参数是创建的求和函数，第二个传入参数是元组构成的列表。`starmap`函数接着会将每一个元组传入求和函数，然后将结果返回到迭代器。

#### takewhile(断言, 可迭代对象)

&emsp;&emsp;`takewhile`模块与`dropwhile`迭代器刚好相反。`takewhile`所创建的迭代器，一旦可迭代对象的断言或过滤器结果为`True`，就返回元素。

``` python
>>> from itertools import takewhile
>>> list(takewhile(lambda x: x < 5, [1,4,6,4,1]))
[1, 4]
```

运行`takewhile`时传入了一个`lambda`函数和一个列表，输出的仅是可迭代对象的前两个整数元素。因为`1`和`4`都小于`5`，而`6`大于`5`，一旦`takewhile`遇到`6`，判定条件结果将是`False`，可迭代对象余下的元素将会被忽略。

#### tee(可迭代对象, n=2)

&emsp;&emsp;`tee`工具能够从一个可迭代对象创建`n`个迭代器，这意味着你能够用一个可迭代对象创建多个迭代器。

``` python
from itertools import tee

data = 'ABC'
iter1, iter2 = tee(data)

for item in iter1:
    print(item)

print("----------")

for item in iter2:
    print(item)
```

执行结果：

``` python
A
B
C
----------
A
B
C
```

这里创建了一个`5`个字母组成的字符串，然后传入`tee`。由于`tee`的默认参数是`2`，所以用两个变量接收`tee`返回的两个迭代器。最后，分别遍历了这两个迭代器，并打印出它们的内容。如你所见，它们的内容是相同的。

#### zip_longest(*可迭代对象, 填充值=None)

&emsp;&emsp;`zip_longest`迭代器可以用于将两个可迭代对象配对。如果可迭代对象的长度不同，也可以传入填充值。

``` python
from itertools import zip_longest

for item in zip_longest('ABCD', 'xy', fillvalue='BLANK'):
    print(item)
```

执行结果：

``` python
('A', 'x')
('B', 'y')
('C', 'BLANK')
('D', 'BLANK')
```

先导入`zip_longest`，然后传入两个需要配对的字符串。你会发现，第一个字符串有`4`个字符，而第二个只有`2`个字符。我们还传入了填充值`BLANK`。当遍历并打印的时候，会看到返回的是元组。前两个元组是分别来自两个字符串第一个和第二个字母的组合，后两个则插入了填充值。
&emsp;&emsp;需要注意的是，如果传入`zip_longest`的可迭代对象是无限的，那么你应该用类似`islice`的工具在外部处理函数，以便控制调用的次数。

### 组合生成器

&emsp;&emsp;`itertools`库提供了四个可用于生成数据排列和组合的迭代器。

#### combinations(可迭代对象, r)

&emsp;&emsp;如果你需要生成数据的组合，`Python`已经为你准备了`itertools.combinations`。`combinations`能够让你通过有一定长度的可迭代对象创建一个迭代器。

``` python
>>> from itertools import combinations
>>> list(combinations('WXYZ', 2))
[('W', 'X'), ('W', 'Y'), ('W', 'Z'), ('X', 'Y'), ('X', 'Z'), ('Y', 'Z')]
```

当你运行这段代码时会发现，`combinations`返回组合是元组。为了使输出结果更具可读性，可以遍历迭代器将元组组合成字符串：

``` python
from itertools import combinations

for item in combinations('WXYZ', 2):
    print(''.join(item))
```

执行结果：

``` python
WX
WY
WZ
XY
XZ
YZ
```

注意，`combinations`函数得到的组合是依循词典顺序的，因而如果传入的可迭代对象已经排序，那么你得到的组合元组也将是按序的。还有一点值得注意，`combinations`函数不会将每个单独的元素自己跟自己组合。

#### combinations_with_replacement(可迭代对象, r)

&emsp;&emsp;`combinationa_with_replacement`迭代器与`combinations`非常类似。唯一的区别是，它会创建元素自己与自己的组合。

``` python
from itertools import combinations_with_replacement

for item in combinations_with_replacement('WXYZ', 2):
    print(''.join(item))
```

#### product(*可迭代对象, 重复=1)

&emsp;&emsp;`product`用于根据一系列输入的可迭代对象计算笛卡尔积。

``` python
>>> from itertools import product
>>> arrays = [(-1,1), (-3,3), (-5,5)]
>>> cp = list(product(*arrays))
>>> cp
[(-1, -3, -5),
 (-1, -3, 5),
 (-1, 3, -5),
 (-1, 3, 5),
 (1, -3, -5),
 (1, -3, 5),
 (1, 3, -5),
 (1, 3, 5)]
```

先导入`product`，然后创建一个元组构成的列表，用变量`arrays`储存，接下来将这些数组传入`product`函数，然后调用。你应该注意到，我们调用时使用了`*arrays`，这会使传入的列表被拆解，依次应用于`product`函数。这意味着你传入的将是三个变量而不是一个。

#### permutations(可迭代变量, r)

&emsp;&emsp;`permutation`能够返回由传入的可迭代对象的元素构成的长度为`r`的排列。与`combination`函数类似，`permutation`将以词典序输出。

``` python
from itertools import permutations

for item in permutations('WXYZ', 2):
    print(''.join(item))
```

执行结果：

``` python
WX
WY
WZ
XW
XY
XZ
YW
YX
YZ
ZW
ZX
ZY
```

容易发现，输出结果比`combinations`的输出结果长一些。`permutation`函数将遍历字符串的所有排列，但当输入元素是唯一的时候，不会让这个元素自己和自己排列。