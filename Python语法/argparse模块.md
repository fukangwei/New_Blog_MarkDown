---
title: argparse模块
categories: Python语法
abbrlink: d338ca5c
date: 2019-02-10 15:02:32
---
&emsp;&emsp;`argparse`是`Python`内置的一个用于命令项选项与参数解析的模块，通过在程序中定义好我们需要的参数，`argparse`将会从`sys.argv`中解析出这些参数，并自动生成帮助和使用信息。

### 简单示例

&emsp;&emsp;先来看一个简单示例，主要有三个步骤：

1. 创建`ArgumentParser`对象。
2. 调用`add_argument`方法添加参数。
3. 使用`parse_args`解析添加的参数。

``` python
import argparse
​
parser = argparse.ArgumentParser()
parser.add_argument('integer', type=int, help='display an integer')
args = parser.parse_args()
print(args.integer)
```

将上面的代码保存为文件`my_test.py`，并在终端运行：

``` bash
$ python my_test.py
usage: my_test.py [-h] integer
my_test.py: error: the following arguments are required: integer
$ python my_test.py abcd
usage: my_test.py [-h] integer
my_test.py: error: argument integer: invalid int value: 'abcd'
$ python my_test.py -h
usage: my_test.py [-h] integer
​
positional arguments:
  integer     display an integer
​
optional arguments:
  -h, --help  show this help message and exit
$ python my_test.py 10
10
```

### 定位参数

&emsp;&emsp;上面的示例其实就演示了定位参数的使用，我们再来看一个例子(计算一个数的平方)：

``` python
import argparse
​
parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number", type=int)
args = parser.parse_args()
print(args.square ** 2)
```

执行结果：

``` bash
$ python my_test.py 9
81
```

### 可选参数

&emsp;&emsp;所谓可选参数，就是命令行参数是可选的：

``` python
import argparse
​
parser = argparse.ArgumentParser()
parser.add_argument("--square", help="display a square of a given number", type=int)
parser.add_argument("--cubic", help="display a cubic of a given number", type=int)
args = parser.parse_args()
​
if args.square:
    print(args.square ** 2)
​
if args.cubic:
    print(args.cubic ** 3)
```

执行结果：

``` bash
$ python my_test.py --h
usage: my_test.py [-h] [--square SQUARE] [--cubic CUBIC]
​
optional arguments:
  -h, --help       show this help message and exit
  --square SQUARE  display a square of a given number
  --cubic CUBIC    display a cubic of a given number
$ python my_test.py --square 8
64
$ python my_test.py --cubic 8
512
$ python my_test.py 8
usage: my_test.py [-h] [--square SQUARE] [--cubic CUBIC]
my_test.py: error: unrecognized arguments: 8
$ python my_test.py  # 没有输出
```

### 混合使用

&emsp;&emsp;定位参数和选项参数可以混合使用，下面一个例子输出给定整数序列的和或最大值(默认)：

``` python
import argparse
​
parser = argparse.ArgumentParser(description='Process some integers.')
parser.add_argument('integers', metavar='N', type=int, nargs='+', \
                    help='an integer for the accumulator')
parser.add_argument('--sum', dest='accumulate', action='store_const', const=sum, \
                    default=max, help='sum the integers (default: find the max)')
args = parser.parse_args()
print(args.accumulate(args.integers))
```

执行结果：

``` bash
$ python my_test.py
usage: my_test.py [-h] [--sum] N [N ...]
my_test.py: error: the following arguments are required: N
$ python my_test.py 1 2 3 4
4
$ python my_test.py 1 2 3 4 --sum
10
```

### add_argument方法

&emsp;&emsp;`add_argument`方法定义如何解析命令行参数：

``` python
ArgumentParser.add_argument(
    name or flags [, action] [, nargs] [, const] [, default] [, type] \
    [, choices] [, required] [, help] [, metavar] [, dest])
```

- `name or flags`：这是必须的参数，它接受选项参数或者是位置参数：

``` python
parser.add_argument('-f', '--foo')  # 选项参数
parser.add_argument('bar')  # 位置参数
```

- `nargs`：当选项后接受多个或者`0`个参数时需要使用这个来指定，比如`-u`选项接受`2`个参数：

``` python
>>> parser.add_argument('-u', nargs=2)  
>>> parser.parse_args('-u a b'.split())  
Namespace(u=['a', 'b'])  
```

当选项接受`1`个或者不需要参数时，指定`nargs='?'`；当没有参数时，会从`default`中取值。对于选项参数有一个额外的情况，就是出现选项而后面没有跟具体参数，那么会从`const`中取值：

``` python
>>> parser.add_argument('-u', nargs='?')
>>> parser.parse_args(''.split())
Namespace(u=None)
>>> parser.parse_args('-u a'.split())
Namespace(u='a')
>>> parser.add_argument('-u', nargs='?', default='d')
>>> parser.add_argument('A', nargs='?', default='e')
>>> parser.parse_args(''.split())
Namespace(A='e', u='d')
>>> parser.parse_args('-u'.split())
Namespace(A='e', u=None)
>>> parser.add_argument('-u', nargs='?', default='d', const='s')
>>> parser.add_argument('A', nargs='?', default='T', const='P')
>>> parser.parse_args(''.split())
Namespace(A='T', u='d')
>>> parser.parse_args('-u'.split())
Namespace(A='T', u='s')
>>> parser.parse_args('A'.split())
Namespace(A='A', u='d')
```

而对于后面需要跟多个参数的情况，例如`-foo a1 a2 a3...`，则需要设置`nargs='*'`：

``` python
>>> parser.add_argument('-u', nargs='*')
>>> parser.parse_args('-u a b c d e'.split())
Namespace(u=['a', 'b', 'c', 'd', 'e'])
```

`nargs='+'`和`nargs='*'`一样，但是当`nargs='+'`时，如果没有参数，位置参数会报错误：

``` python
>>> parser.add_argument('u', nargs='+')
>>> parser.parse_args(''.split())
usage: [-h] u [u ...] : error: too few arguments
```

而`*`会使用默认值：

``` python
>>> parser.add_argument('u', nargs='*', default='e')
>>> parser.parse_args(''.split())
Namespace(u='e')
```

- `default`：当参数需要默认值时，由这个参数指定，默认为`None`。当`default=argparse.SUPPRESS`时，不使用任何值：

``` python
>>> parser.add_argument('u', nargs='*', default=argparse.SUPPRESS)
>>> parser.parse_args(''.split())
Namespace()
```

- `type`：使用这个参数可以转换输入参数的具体类型。该参数可以关联到某个自定义的处理函数，这种函数通常用来检查值的范围以及合法性：

``` python
>>> parser.add_argument('-u', type=int)
>>> parser.add_argument('f', type=file)
>>> parser.parse_args('-u 2 aa'.split())
Namespace(f='aa', mode 'r' at 0x8b4ee38>, u=2)
```

- `choices`：这个参数用来检查输入参数的范围：

``` python
>>> parser.add_argument('-u', type=int, choices=[1,3,5])
>>> parser.parse_args('-u 3'.split())
Namespace(u=3)
>>> parser.parse_args('-u 4'.split())
usage: [-h] [-u {1,3,5}] : error: argument -u: invalid choice: 4 (choose from 1, 3, 5)
```

- `required`：当某个选项指定需要在命令中出现时，使用这个参数：

``` python
>>> parser.add_argument('-u', required=True)
>>> parser.parse_args(''.split())
usage: [-h] -u U : error: argument -u is required
```

- `help`：使用这个参数描述选项作用：

``` python
>>> parser.add_argument('-u', required=True, default='wowo',
... help='%(prog)s for test sth(default: %(default)s)')
>>> parser.print_help()
usage: [-h] -u U
​
optional arguments:
  -h, --help  show this help message and exit
  -u U        for test sth(default: wowo)
```

- `dest`：这个参数相当于把位置或者选项关联到一个特定的名字：

``` python
>>> parser.add_argument('--str', nargs='*')
>>> parser.parse_args('--str a b c'.split())
Namespace(str=['a', 'b', 'c'])
>>> parser.add_argument('--str', nargs='*', dest='myname')
>>> parser.parse_args('--str a b c'.split())
Namespace(myname=['a', 'b', 'c'])
```

- `metavar`：这个参数用于`help`信息输出：

``` python
>>> parser.add_argument('--str', nargs='*', metavar='AAA')
>>> parser.print_help()
usage: [-h] [--str [AAA [AAA ...]]]
​
optional arguments:
  -h, --help           show this help message and exit
  --str [AAA [AAA ...]]
>>> parser.add_argument('str', nargs='*', metavar='AAA')
>>> parser.print_help()
usage: [-h] [AAA [AAA ...]]
​
positional arguments:
  AAA
​
optional arguments:
  -h, --help  show this help message and exit
```