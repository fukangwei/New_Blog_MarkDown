---
title: h5py模块
date: 2019-02-10 15:47:38
categories: Python语法
---
&emsp;&emsp;`h5py`文件是存放两类对象的容器，分别是数据集(`dataset`)和组(`group`)。`dataset`类似于`numpy`的数组；`group`则类似于`python`中的字典，有键(`key`)和值(`value`)。`键`就是组成员的名称，`值`就是组成员对象本身(组或者数据集)。`group`中可以存放`dataset`或者其他的`group`。下面来看如何创建组和数据集。
&emsp;&emsp;1. 创建一个`h5py`文件：

``` python
import h5py
f = h5py.File("myh5py.hdf5", "w")  # 如果是读取文件的话，就把“w”换成“r”
```

&emsp;&emsp;2. 创建`dataset`数据集：

``` python
import h5py
​
f = h5py.File("myh5py.hdf5", "w")
# “deset1”是数据集的name，(20,)代表数据集的shape，“i”代表的是数据集的元素类型
f.create_dataset("dset1", (20,), 'i')
​
for key in f.keys():
    print(key)
    print(f[key].name)
    print(f[key].shape)
    print(f[key].value)
```

执行结果：

``` python
dset1
/dset1
(20,)
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

这里仅仅创建了一个存放`20`个整型元素的数据集，并没有赋值，默认全是`0`。如何赋值呢？看下面的代码：

``` python
import h5py
import numpy as np
​
f = h5py.File("myh5py.hdf5", "w")
d1 = f.create_dataset("dset1", (20,), 'i')
d1[...] = np.arange(20)  # 赋值
# 或者可以直接按照下面的方式创建数据集并赋值
f["dset2"] = np.arange(15)
​
for key in f.keys():
    print(f[key].name)
    print(f[key].value)
```

执行结果：

``` python
/dset1
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]
/dset2
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14]
```

如果我们有现成的`numpy`数组，那么就可以在创建数据集的时候赋值，此时就不必指定数据的类型和形状，只需要把数组名传给参数`data`：

``` python
import h5py
import numpy as np
​
f = h5py.File("myh5py.hdf5", "w")
a = np.arange(20)
d1 = f.create_dataset("dset1", data=a)
​
for key in f.keys():
    print(f[key].name)
    print(f[key].value)
```

执行结果：

``` python
/dset1
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]
```

&emsp;&emsp;3. 创建`group`组：

``` python
import h5py
import numpy as np
​
f = h5py.File("myh5py.hdf5", "w")
g1 = f.create_group("bar")  # 创建一个名为bar的组
# 在bar这个组里面分别创建name为dset1和dset2的数据集并赋值
g1["dset1"] = np.arange(10)
g1["dset2"] = np.arange(12).reshape((3, 4))
​
for key in g1.keys():
    print(g1[key].name)
    print(g1[key].value)
```

注意观察数据集`dset1`和`dset2`的名字是不是有点和前面的不一样，如果是直接创建数据集，不在任何组里面，那么它的名字就是`/+名字`；现在这两个数据集都在`bar`这个`group`里面，名字就变成了`/bar+/名字`，是不是有点文件夹的感觉。

---

### Core concepts

&emsp;&emsp;An `HDF5` file is a container for two kinds of objects: datasets, which are `array-like` collections of data, and groups, which are `folder-like` containers that hold datasets and other groups. The most fundamental thing to remember when using `h5py` is: Groups work like dictionaries, and datasets work like `NumPy` arrays.
&emsp;&emsp;Suppose someone has sent you a `HDF5` file, `mytestfile.hdf5`. The very first thing you'll need to do is to open the file for reading:

``` python
>>> import h5py
>>> f = h5py.File('mytestfile.hdf5', 'r')
```

The `File` object is your starting point. What is stored in this file? Remember `h5py.File` acts like a Python dictionary, thus we can check the keys:

``` python
>>> f.keys()
[u'mydataset']
```

Based on our observation, there is one data set, mydataset in the file. Let us examine the data set as a Dataset object:

``` python
>>> dset = f['mydataset']
```

The object we obtained isn't an array, but an `HDF5` dataset. Like `NumPy` arrays, datasets have both a shape and a data type:

``` python
>>> dset.shape
(100,)
>>> dset.dtype
dtype('int32')
```

They also support `array-style` slicing. This is how you read and write data from a dataset in the file:

``` python
>>> dset[...] = np.arange(100)
>>> dset[0]
0
>>> dset[10]
10
>>> dset[0:100:10]
array([ 0, 10, 20, 30, 40, 50, 60, 70, 80, 90])
```

### Appendix: Creating a file

&emsp;&emsp;At this point, you may wonder how `mytestdata.hdf5` is created. We can create a file by setting the mode to `w` when the `File` object is initialized. Some other modes are a (for `read/write/create` access), and `r+` (for `read/write` access). A full list of file access modes and their meanings is at `File` Objects.

``` python
>>> import h5py
>>> import numpy as np
>>> f = h5py.File("mytestfile.hdf5", "w")
```

The `File` object has a couple of methods which look interesting. One of them is `create_dataset`, which as the name suggests, creates a data set of given shape and dtype:

``` python
>>> dset = f.create_dataset("mydataset", (100,), dtype='i')
The File object is a context manager; so the following code works too:
>>> import h5py
>>> import numpy as np
>>> with h5py.File("mytestfile.hdf5", "w") as f:
...     dset = f.create_dataset("mydataset", (100,), dtype='i')
```

### Groups and hierarchical organization

&emsp;&emsp;`HDF` stands for `Hierarchical Data Format`. Every object in an `HDF5` file has a name, and they're arranged in a `POSIX-style` hierarchy with `/-separators`:

``` python
>>> dset.name
u'/mydataset'
```

The `folders` in this system are called groups. The `File` object we created is itself a group, in this case the root group, named `/`:

``` python
>>> f.name
u'/'
```

Creating a subgroup is accomplished via the `aptly-named` `create_group`. But we need to open the file in `read/write` mode first:

``` python
>>> f = h5py.File('mydataset.hdf5', 'r+')
>>> grp = f.create_group("subgroup")
```

All Group objects also have the `create_*` methods like `File`:

``` python
>>> dset2 = grp.create_dataset("another_dataset", (50,), dtype='f')
>>> dset2.name
u'/subgroup/another_dataset'
```

By the way, you don't have to create all the intermediate groups manually. Specifying a full path works just fine:

``` python
>>> dset3 = f.create_dataset('subgroup2/dataset_three', (10,), dtype='i')
>>> dset3.name
u'/subgroup2/dataset_three'
```

Groups support most of the Python `dictionary-style` interface. You retrieve objects in the file using the `item-retrieval` syntax:

``` python
>>> dataset_three = f['subgroup2/dataset_three']
Iterating over a group provides the names of its members: 
>>> for name in f:
...     print name
mydataset
subgroup
subgroup2
```

Membership testing also uses names:

``` python
>>> "mydataset" in f
True
>>> "somethingelse" in f
False
```

You can even use full path names:

``` python
>>> "subgroup/another_dataset" in f
True
```

There are also the familiar `keys()`, `values()`, `items()` and `iter()` methods, as well as `get()`.
&emsp;&emsp;Since iterating over a group only yields its `directly-attached` members, iterating over an entire file is accomplished with the Group methods `visit()` and `visititems()`, which take a callable:

``` python
>>> def printname(name):
...     print name
>>> f.visit(printname)
mydataset
subgroup
subgroup/another_dataset
subgroup2
subgroup2/dataset_three
```

### Attributes

&emsp;&emsp;One of the best features of `HDF5` is that you can store metadata right next to the data it describes. All groups and datasets support attached named bits of data called attributes.
&emsp;&emsp;Attributes are accessed through the attrs proxy object, which again implements the dictionary interface:

``` python
>>> dset.attrs['temperature'] = 99.5
>>> dset.attrs['temperature']
99.5
>>> 'temperature' in dset.attrs
True
```