---
title: h5py模块
categories: Python语法
date: 2019-02-10 15:47:38
---
&emsp;&emsp;`h5py`文件是存放`2`类对象的容器，分别是数据集(`dataset`)和组(`group`)。<!--more-->

- `dataset`类似于`numpy`的数组。
- `group`类似于`python`中的字典，有键(`key`)和值(`value`)。

### 创建文件

&emsp;&emsp;创建一个`h5py`文件：

``` python
import h5py
f = h5py.File("myh5py.hdf5", "w")  # 如果是读取文件的话，就把“w”换成“r”
```

### 创建数据集

&emsp;&emsp;创建`dataset`数据集：

``` python
import h5py
import numpy as np

f = h5py.File("myh5py.hdf5", "w")
# “dset1”是数据集的name，(20,)代表数据集的shape，“i”代表的是数据集的元素类型
d1 = f.create_dataset("dset1", (20,), 'i')
d1[...] = np.arange(20)  # 赋值
# 可以直接按照下面的方式创建数据集并赋值
f["dset2"] = np.arange(15)

for key in f.keys():
    print(f[key].name)
    print(f[key][()])
```

执行结果：

``` python
/dset1
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]
/dset2
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14]
```

&emsp;&emsp;如果有现成的`numpy`数组，在创建数据集的时候，只需要把数组名传给参数`data`即可：

``` python
import h5py
import numpy as np

f = h5py.File("myh5py.hdf5", "w")
a = np.arange(20)
d1 = f.create_dataset("dset1", data=a)

for key in f.keys():
    print(f[key].name)
    print(f[key][()])
```

执行结果：

``` python
/dset1
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]
```

### 创建组

&emsp;&emsp;创建`group`组：

``` python
import h5py
import numpy as np

f = h5py.File("myh5py.hdf5", "w")
g1 = f.create_group("bar")  # 创建一个名为bar的组
# 在bar这个组里面，分别创建name为dset1和dset2的数据集并赋值
g1["dset1"] = np.arange(10)
g1["dset2"] = np.arange(12).reshape((3, 4))

for key in g1.keys():
    print(g1[key].name)
    print(g1[key][()])
```

执行结果：

``` python
/bar/dset1
[0 1 2 3 4 5 6 7 8 9]
/bar/dset2
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
```