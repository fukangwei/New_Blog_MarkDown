---
title: operator模块
categories: Python语法
abbrlink: 36dff267
date: 2018-12-28 10:43:00
---
&emsp;&emsp;本模块主要包括一些`Python`内部操作符对应的函数。这些函数主要分为几类：`对象比较`、`逻辑比较`、`算术运算`和`序列操作`：<!--more-->

操作        | 语法                     | 函数
------------|-------------------------|----
相加        | `a + b`                 | `add(a, b)`
字符串拼接   | `seq1 + seq2`           | `concat(seq1, seq2)`
包含测试     | `obj in seq`            | `contains(seq, obj)`
普通除法     | `a / b`                 | `truediv(a, b)`
取整除法     | `a // b`                | `floordiv(a, b)`
按位与       | `a & b`                 | `and_(a, b)`
按位异或     | `a ^ b`                 | `xor(a, b)`
按位取反     | `~ a`                   | `invert(a)`
按位或       | <code>a &#124; b</code> | `or_(a, b)`
指数运算     | `a ** b`                | `pow(a, b)`
识别         | `a is b`                | `is_(a, b)`
识别         | `a is not b`            | `is_not(a, b)`
索引赋值     | `obj[k] = v`            | `setitem(obj, k, v)`
索引删除     | `del obj[k]`            | `delitem(obj, k)`
索引         | `obj[k]`                | `getitem(obj, k)`
左移         | `a << b`                | `lshift(a, b)`
取模         | `a % b`                 | `mod(a, b)`
乘法         | `a * b`                 | `mul(a, b)`
负数         | `- a`                   | `neg(a)`
非运算       | `not a`                 | `not_(a)`
正数         | `+ a`                   | `pos(a)`
右移运算     | `a >> b`                 | `rshift(a, b)`
切片赋值     | `seq[i:j] = values`      | `setitem(seq, slice(i, j), values)`
切片删除     | `del seq[i:j]`           | `delitem(seq, slice(i, j))`
切片        | `seq[i:j]`               | `getitem(seq, slice(i, j))`
字符串格式化 | `s % obj`                | `mod(s, obj)`
减法        | `a - b`                  | `sub(a, b)`
真值测试    | `obj`                    | `truth(obj)`
小于        | `a < b`                  | `lt(a, b)`
小于等于    | `a <= b`                 | `le(a, b)`
等于        | `a == b`                 | `eq(a, b)`
不等于      | `a != b`                 | `ne(a, b)`
大于等于    | `a >= b`                 | `ge(a, b)`
大于        | `a > b`                  | `gt(a, b)`

示例代码如下：

``` python
import operator
​
a = operator.add(1, 3)
print(a)  # 结果为4
```

&emsp;&emsp;对于`operator`模块中的`itemgetter`函数，`help`信息如下：

``` bash
>>> import operator
>>> help(operator.itemgetter)
Help on class itemgetter in module operator:
​
class itemgetter(builtins.object)
 |  itemgetter(item, ...) --> itemgetter object
 |
 |  Return a callable object that fetches the given item(s) from its operand.
 |  After f = itemgetter(2), the call f(r) returns r[2].
 |  After g = itemgetter(2, 5, 3), the call g(r) returns (r[2], r[5], r[3])
 |
 |  Methods defined here:
 |
 |  __call__(self, /, *args, **kwargs)
 |      Call self as a function.
 |
 |  __getattribute__(self, name, /)
 |      Return getattr(self, name).
 |
 |  __new__(*args, **kwargs) from builtins.type
 |      Create and return a new object.  See help(type) for accurate signature.
 |
 |  __reduce__(...)
 |      Return state information for pickling
 |
 |  __repr__(self, /)
 |      Return repr(self).
```

示例如下：

``` python
a = [1,2,3]
>>> b = operator.itemgetter(1)  # 定义函数b，获取对象的第1个域的值
>>> b(a)
2
>>> b = operator.itemgetter(1, 0)  # 定义函数b，获取对象的第1个域和第0个域的值
>>> b(a)
(2, 1)
```