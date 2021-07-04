---
title: bisect模块
categories: Python语法
date: 2019-02-09 16:18:36
---
&emsp;&emsp;`bisect`模块实现在已排序的列表插入元素之后，仍然保持排序。<!--more-->

### bisect_left

&emsp;&emsp;函数原型如下：

``` python
bisect.bisect_left(a, x, lo=0, hi=len(a))
```

- 函数返回元素`x`适合在列表`a`中插入的位置。
- 如果元素`x`已经在列表`a`中，那么插入的位置在元素`x`的左边。

``` python
import bisect

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

### bisect_right

&emsp;&emsp;函数原型如下：

``` python
bisect.bisect_right(a, x, lo=0, hi=len(a))
bisect.bisect(a, x, lo=0, hi=len(a))
```

- 函数返回元素`x`适合在列表`a`中插入的位置。
- 如果元素`x`已经在列表`a`中，那么插入的位置在元素`x`的右边。

``` python
import bisect

l = [8, 5, 6, 6, 3]
l.sort()
print(l)
print(bisect.bisect_right(l, 0))
print(l)
print(bisect.bisect_right(l, 5))
print(l)
print(bisect.bisect_right(l, 7))
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

### insort_left

&emsp;&emsp;函数原型如下：

``` python
bisect.insort_left(a, x, lo=0, hi=len(a))
```

- 插入元素`x`到列表`a`中，并把列表排序。
- 如果发现有相同元素，就插入到相同元素的左边。

``` python
import bisect

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

### insort_right

&emsp;&emsp;函数原型如下：

``` python
bisect.insort_right(a, x, lo=0, hi=len(a))
bisect.insort(a, x, lo=0, hi=len(a))
```

- 插入元素`x`到列表`a`中，并把列表排序。
- 如果发现有相同元素，就插入到相同元素的右边。

``` python
import bisect

l = [8, 5, 6, 6, 3]
l.sort()
print(l)
bisect.insort_right(l, 0)
print(l)
bisect.insort_right(l, 5)
print(l)
bisect.insort_right(l, 7)
print(l)
```

执行结果：

``` python
[3, 5, 6, 6, 8]
[0, 3, 5, 6, 6, 8]
[0, 3, 5, 5, 6, 6, 8]
[0, 3, 5, 5, 6, 6, 7, 8]
```