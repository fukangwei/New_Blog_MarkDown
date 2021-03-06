---
title: copy模块
categories: Python语法
date: 2019-03-02 18:52:20
---
### 拷贝比较

&emsp;&emsp;拷贝有`=`、`copy.copy`和`copy.deepcopy`这`3`种方式：<!--more-->

- `b = a`：赋值引用，`a`和`b`都指向同一个对象：

<img src="./copy模块/1.png" width=25%>

- `b = a.copy()`：浅拷贝，`a`和`b`是一个独立的对象，但它们的子对象还是指向统一对象：

<img src="./copy模块/2.png" width=25%>

- `b = copy.deepcopy(a)`：深度拷贝，`a`和`b`完全拷贝了父对象及其子对象，两者是完全独立的：

<img src="./copy模块/3.png" width=25%>

### 代码实例

&emsp;&emsp;代码实例：

``` python
import copy

a = [1, 2, 3, 4, ['a', 'b']]  # 原始对象
b = a  # 赋值，传对象的引用
c = copy.copy(a)  # 对象拷贝，浅拷贝
d = copy.deepcopy(a)  # 对象拷贝，深拷贝
a.append(5)  # 修改对象a
a[4].append('c')  # 修改对象a中的['a', 'b']数组对象

print('a = ', a)
print('b = ', b)
print('c = ', c)
print('d = ', d)
```

执行结果：

``` python
a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
c =  [1, 2, 3, 4, ['a', 'b', 'c']]
d =  [1, 2, 3, 4, ['a', 'b']]
```