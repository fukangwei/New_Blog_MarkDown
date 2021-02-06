---
title: collections模块
categories: Python语法
date: 2019-01-09 14:04:31
---
&emsp;&emsp;`collections`是`Python`内建的一个集合模块，提供了许多有用的集合类。<!--more-->

### namedtuple

&emsp;&emsp;`namedtuple`是一个函数，它用来创建一个自定义的元组对象，并且规定了元组元素的个数，并可以用属性而不是索引来引用元组的某个元素。可以通过`namedtuple`来定义一种数据类型，它具备元组的不变性，又可以根据属性来引用，十分方便。

``` python
namedtuple(typename, filed_name, verbose=False, rename=False);
```

参数`typename`是该元组的名字，`field_name`是该元组成员的名字，`verbose`指示是否输出构造的源码，`rename`指示是否在`field_name`非法时进行重命名。

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

&emsp;&emsp;使用`list`存储数据时，按照索引访问元素很快，但是插入和删除元素就很慢了，因为`list`是线性存储，数据量大的时候，插入和删除效率很低。`deque`是为了高效实现插入和删除操作的双向列表，适合用于队列和栈。

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

&emsp;&emsp;使用字典时，如果引用的`Key`不存在，就会抛出`KeyError`。如果希望`key`不存在时，返回一个默认值，就可以用`defaultdict`。

``` python
from collections import defaultdict

Mydict = defaultdict(lambda: 'N/A')
Mydict['key1'] = 'abc'
print(Mydict['key1'])  # 字典的key1存在
print(Mydict['key2'])  # 字典的key2不存在，返回默认值为“N/A”
```

执行结果：

``` python
abc
N/A
```

注意默认值是调用函数返回的，而函数在创建`defaultdict`对象时传入。除了`key`不存在而返回默认值，其他功能与普通字典无异。

### OrderedDict

&emsp;&emsp;它是有序字典的应用。`OrderedDict`的有序性是按照插入的顺序，而不是`key`的顺序。

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

通过`OrderedDict`可以实现一个`FIFO`的字典，当容量超出限制后，先删除最早添加的`key`：

``` python
from collections import OrderedDict

class LastUpdatedOrderedDict(OrderedDict):
    def __init__(self, capacity):
        super(LastUpdatedOrderedDict, self).__init__()
        self._capacity = capacity

    def __setitem__(self, key, value):
        containsKey = 1 if key in self else 0

        if len(self) - containsKey >= self._capacity:
            last = self.popitem(last=False)
            print('remove:', last)

        if containsKey:
            del self[key]
            print('set:', (key, value))
        else:
            print('add:', (key, value))

        OrderedDict.__setitem__(self, key, value)
```

### Counter

&emsp;&emsp;简单的计数器，例如统计字符出现的个数：

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


---

### collections.defaultdict

&emsp;&emsp;有没有一种字典本身提供了默认值的功能呢？答案是肯定的，那就是`collections.defaultdict`。`defaultdict`类与`dict`相似，但它是使用一个类型来初始化的：

``` python
from collections import defaultdict

dd = defaultdict(list)
print(dd)
```

执行结果：

``` python
defaultdict(<class 'list'>, {})
```

`defaultdict`类的初始化函数接受一个类型作为参数，当所访问的键不存在的时候，可以实例化一个值作为默认值：

``` python
from collections import defaultdict

dd = defaultdict(list)
print(dd['foo'])
print(dd)
dd['bar'].append('quux')
print(dd)
```

执行结果：

``` python
[]
defaultdict(<class 'list'>, {'foo': []})
defaultdict(<class 'list'>, {'foo': [], 'bar': ['quux']})
```