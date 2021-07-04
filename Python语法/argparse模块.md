---
title: argparse模块
categories: Python语法
date: 2019-02-10 15:02:32
---
&emsp;&emsp;`argparse`用于命令项选项与参数解析，它将会从`sys.argv`中解析出这些参数，并自动生成帮助和使用信息。<!--more-->

### 定位参数

&emsp;&emsp;先来看一个简单示例，主要有`3`个步骤：

1. 创建`ArgumentParser`对象。
2. 调用`add_argument`方法添加参数。
3. 使用`parse_args`解析添加的参数。

``` python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number", type=int)
args = parser.parse_args()
print(args.square ** 2)
```

执行结果：

``` bash
$ python test_python.py -h
usage: test_python.py [-h] square

positional arguments:
  square      display a square of a given number

optional arguments:
  -h, --help  show this help message and exit
$ python my_test.py 9
81
```

### 可选参数

&emsp;&emsp;所谓`可选参数`，就是命令行参数是可选的：

``` python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--square", help="display a square of a given number", type=int)
parser.add_argument("--cubic", help="display a cubic of a given number", type=int)
args = parser.parse_args()

if args.square:
    print(args.square ** 2)

if args.cubic:
    print(args.cubic ** 3)
```

执行结果：

``` bash
$ python my_test.py -h
usage: my_test.py [-h] [--square SQUARE] [--cubic CUBIC]

optional arguments:
  -h, --help       show this help message and exit
  --square SQUARE  display a square of a given number
  --cubic CUBIC    display a cubic of a given number
$ python my_test.py --square 8
64
$ python my_test.py --cubic 8
512
```

### 混合使用

&emsp;&emsp;定位参数和选项参数可以混合使用：

``` python
import argparse

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
$ python test_python.py -h
usage: test_python.py [-h] [--sum] N [N ...]

Process some integers.

positional arguments:
  N           an integer for the accumulator

optional arguments:
  -h, --help  show this help message and exit
  --sum       sum the integers (default: find the max)
$ python my_test.py 1 2 3 4
4
$ python my_test.py 1 2 3 4 --sum
10
```