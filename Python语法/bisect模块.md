---
title: bisect模块
categories: Python语法
abbrlink: a9dc8706
date: 2019-02-09 16:18:36
---
&emsp;&emsp;本模块实现已经排序的队列列表插入元素之后保持排序。对于个大量数据的列表来看，插入元素并保持排序，计算量是非常大的。本模块实现了`bisect`算法，主要基于二分算法来实现。<!--more-->
&emsp;&emsp;`bisect_left`函数原型如下：

``` python
bisect.bisect_left(a, x, lo=0, hi=len(a))
```

对有序列表`a`里插入元素`x`，保持有序不变，返回插入的位置。参数`lo`和`hi`是表示判断列表的范围，默认是整个范围。如果插入的元素`x`已经在列表`a`存在，那就插入在存在元素的左边。

``` python
import bisect
​
l = [8, 5, 6, 6, 3]
l.sort()
print(l)
print(bisect.bisect_left(l, 0))
print(l)
print(bisect.bisect_left(l, 5))
print(l)
print(bisect.bisect_left(l, 7))
print(l)
```

执行结果：

``` python
[3, 5, 6, 6, 8]
0
[3, 5, 6, 6, 8]
1
[3, 5, 6, 6, 8]
4
[3, 5, 6, 6, 8]
```

&emsp;&emsp;`bisect_right`如下：

``` python
bisect.bisect_right(a, x, lo=0, hi=len(a))
bisect.bisect(a, x, lo=0, hi=len(a))
```

在有序队列`a`里查找`x`可以插入的位置，并保持队列有序。如果插入的值与队列里的值相同，则插入在相同元素的右边，把这个位置的索引值返回。

``` python
import bisect
​
l = [8, 5, 6, 6, 3]
l.sort()
print(l)
print(bisect.bisect_right(l, 0))
print(l)
print(bisect.bisect(l, 5))
print(l)
print(bisect.bisect(l, 7))
print(l)
```

执行结果：

``` python
[3, 5, 6, 6, 8]
0
[3, 5, 6, 6, 8]
2
[3, 5, 6, 6, 8]
4
[3, 5, 6, 6, 8]
```

&emsp;&emsp;`insort_left`如下：

``` python
bisect.insort_left(a, x, lo=0, hi=len(a))
```

插入一个元素`x`到队列`a`，并把队列排序，相当于`a.insert(bisect.bisect_left(a, x, lo, hi), x)`。

``` python
import bisect
​
l = [8, 5, 6, 6, 3]
l.sort()
print(l)
bisect.insort_left(l, 0)
print(l)
bisect.insort_left(l, 5)
print(l)
bisect.insort_left(l, 7)
print(l)
```

执行结果：

``` python
[3, 5, 6, 6, 8]
[0, 3, 5, 6, 6, 8]
[0, 3, 5, 5, 6, 6, 8]
[0, 3, 5, 5, 6, 6, 7, 8]
```

&emsp;&emsp;`insort_right`如下：

``` python
bisect.insort_right(a, x, lo=0, hi=len(a))
bisect.insort(a, x, lo=0, hi=len(a))
```

插入元素`x`到`a`队列里，如果发现有相同元素，就插入到相同元素的右边。

``` python
import bisect
​
l = [8, 5, 6, 6, 3]
l.sort()
print(l)
bisect.insort_right(l, 0)
print(l)
bisect.insort(l, 5)
print(l)
bisect.insort(l, 7)
print(l)
```

执行结果：

``` python
[3, 5, 6, 6, 8]
[0, 3, 5, 6, 6, 8]
[0, 3, 5, 5, 6, 6, 8]
[0, 3, 5, 5, 6, 6, 7, 8]
```

对上面的函数进行一些简单地封装：

``` python
import bisect
​
def index(a, x):
    'Locate the leftmost value exactly equal to x'
    i = bisect.bisect_left(a, x)

    if i != len(a) and a[i] == x:
        return i

    raise ValueError
​
def find_lt(a, x):
    'Find rightmost value less than x'
    i = bisect.bisect_left(a, x)

    if i:
        return a[i - 1]

    raise ValueError
​
def find_le(a, x):
    'Find rightmost value less than or equal to x'
    i = bisect.bisect_right(a, x)

    if i:
        return a[i - 1]

    raise ValueError
​
def find_gt(a, x):
    'Find leftmost value greater than x'
    i = bisect.bisect_right(a, x)

    if i != len(a):
        return a[i]

    raise ValueError
​
def find_ge(a, x):
    'Find leftmost item greater than or equal to x'
    i = bisect.bisect_left(a, x)

    if i != len(a):
        return a[i]

    raise ValueError
```

&emsp;&emsp;对学生成绩转换为`ABCDF`四个等级：

``` python
import bisect
​
def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
    i = bisect.bisect(breakpoints, score)
    return grades[i]
​
print([33, 99, 77, 70, 89, 90, 100])
print([grade(score) for score in [33, 99, 77, 70, 89, 90, 100]])
```

执行结果：

``` python
[33, 99, 77, 70, 89, 90, 100]
['F', 'A', 'C', 'C', 'B', 'A', 'A']
```