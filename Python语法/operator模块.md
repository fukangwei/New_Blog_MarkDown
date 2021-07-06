---
title: operator模块
categories: Python语法
date: 2018-12-28 10:43:00
---
### 基础函数

&emsp;&emsp;`operator`模块主要包括一些`Python`内部操作符对应的函数：<!--more-->

操作        | 语法                     | 函数                 | 操作        | 语法                  | 函数
------------|-------------------------|----------------------|-------------|----------------------|-------
相加        | `a + b`                 | `add(a, b)`           | 字符串拼接   | `seq1 + seq2`       | `concat(seq1, seq2)`
包含测试     | `obj in seq`            | `contains(seq, obj)` | 普通除法     | `a / b`             | `truediv(a, b)`
取整除法     | `a // b`                | `floordiv(a, b)`     | 按位与       | `a & b`             | `and_(a, b)`
按位异或     | `a ^ b`                 | `xor(a, b)`          | 按位取反     | `~ a`               | `invert(a)`
按位或       | <code>a &#124; b</code> | `or_(a, b)`          | 指数运算     | `a ** b`            | `pow(a, b)`
识别         | `a is b`                | `is_(a, b)`          | 识别         | `a is not b`       | `is_not(a, b)`
索引赋值     | `obj[k] = v`            | `setitem(obj, k, v)` | 索引删除     | `del obj[k]`        | `delitem(obj, k)`
索引         | `obj[k]`                | `getitem(obj, k)`    | 左移         | `a << b`           | `lshift(a, b)`
取模         | `a % b`                 | `mod(a, b)`          | 乘法         | `a * b`            | `mul(a, b)`
负数         | `- a`                   | `neg(a)`             | 非运算       | `not a`             | `not_(a)`
正数         | `+ a`                   | `pos(a)`             | 右移运算     | `a >> b`            | `rshift(a, b)`
字符串格式化 | `s % obj`                | `mod(s, obj)`        | 切片赋值     | `seq[i:j] = values` | `setitem(seq, slice(i, j), values)`
减法        | `a - b`                  | `sub(a, b)`          | 切片删除     | `del seq[i:j]`      | `delitem(seq, slice(i, j))`
真值测试    | `obj`                    | `truth(obj)`         | 切片        | `seq[i:j]`           | `getitem(seq, slice(i, j))`
小于        | `a < b`                  | `lt(a, b)`           | 小于等于    | `a <= b`             | `le(a, b)`
等于        | `a == b`                 | `eq(a, b)`           | 不等于      | `a != b`             | `ne(a, b)`
大于等于    | `a >= b`                 | `ge(a, b)`           | 大于        | `a > b`              | `gt(a, b)`

&emsp;&emsp;代码实例：

``` python
import operator

a = operator.add(1, 3)
print(a)  # 结果为4
```

### itemgetter函数

&emsp;&emsp;`operator`模块提供的`itemgetter`函数主要用于获取某一对象`特定维度的数据`：

``` python
a = [1, 2, 3]
>>> b = operator.itemgetter(1)  # 定义函数b，获取对象第1维的值
>>> b(a)
2
>>> b = operator.itemgetter(1, 0)  # 定义函数b，获取对象第1维和第0维的值
>>> b(a)
(2, 1)
```