---
title: collections模块
categories: Python语法
date: 2019-01-09 14:04:31
---
### namedtuple

&emsp;&emsp;`namedtuple`用来创建一个自定义的元组对象。<!--more-->

``` python
namedtuple(typename, filed_name, verbose=False, rename=False);
```

- `typename`：元组的名字。
- `field_name`：元组成员的名字。
- `verbose`：是否输出构造的源码。
- `rename`：是否在`field_name`非法时进行重命名。

``` python
from collections import namedtuple

Mytuple = namedtuple('Mytuple', ['x', 'y'])
n = Mytuple(11, 22)
print("n.x =", n.x)
print("n.y =", n.y)
```

执行结果：

``` python
n.x = 11
n.y = 22
```

### deque

&emsp;&emsp;`deque`是一个实现了插入和删除操作的双向列表。

``` python
from collections import deque

q = deque(['a', 'b', 'c'])
q.append('x')  # 默认添加到列表最后一项
q.appendleft('y')  # 添加到列表第一项
print(q)
print(q.pop())  # 默认删除列表最后一个元素
print(q.popleft())  # 删除列表的第一个元素
print(q)
```

执行结果：

``` python
deque(['y', 'a', 'b', 'c', 'x'])
x
y
deque(['a', 'b', 'c'])
```

### defaultdict

&emsp;&emsp;在使用字典时，如果`Key`不存在，就会抛出`KeyError`；如果希望`key`不存在时，返回一个默认值，就可以用`defaultdict`。

``` python
from collections import defaultdict

Mydict = defaultdict(lambda: 'N/A')
Mydict['key1'] = 'abc'
print(Mydict['key1'])  # 字典的key1存在
print(Mydict['key2'])  # 字典的key2不存在，返回默认值“N/A”
```

执行结果：

``` python
abc
N/A
```

### OrderedDict

&emsp;&emsp;`OrderedDict`是一个有序字典，其有序性是指插入的顺序。

``` python
from collections import OrderedDict

d = dict([('a', 1), ('c', 3), ('b', 2)])
print(d)  # dict的Key是无序的
od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
print(od)  # OrderedDict的Key是有序的
```

执行结果：

``` python
{'a': 1, 'c': 3, 'b': 2}
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```

### Counter

&emsp;&emsp;`Counter`是一个简单的计数器。例如，统计字符出现的个数：

``` python
from collections import Counter

c = Counter()

for ch in 'programming':
    c[ch] = c[ch] + 1

print(c)
```

执行结果：

``` python
Counter({'r': 2, 'g': 2, 'm': 2, 'p': 1, 'o': 1, 'a': 1, 'i': 1, 'n': 1})
```