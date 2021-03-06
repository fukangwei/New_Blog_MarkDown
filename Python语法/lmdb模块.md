---
title: lmdb模块
categories: Python语法
date: 2018-12-27 18:49:06
---
&emsp;&emsp;`LMDB`和`SQLite`、`MySQL`等关系型数据库不同，它属于`key-value`数据库，`key`与`value`都是字符串。<!--more-->
&emsp;&emsp;操作`LMDB`的流程如下：

``` python
env = lmdb.open()   # 打开环境
txn = env.begin()   # 建立事务
txn.put(key, value) # 进行插入和修改
txn.delete(key)     # 进行删除
txn.get(key)        # 进行查询
txn.cursor()        # 进行遍历
txn.commit()        # 提交更改
```

### 建立环境

&emsp;&emsp;执行如下代码：

``` python
import lmdb
env = lmdb.open("students")
```

可以看到当前目录下多了`students`目录，里面有`data.mdb`和`lock.mdb`这`2`个文件。

### 插入、删除和修改

&emsp;&emsp;插入与修改都用`put`实现，删除用`delete`实现：

``` python
import lmdb

env = lmdb.open("students")
txn = env.begin(write=True)  # 增加写数据库权限

txn.put(str(1).encode(), "Alice".encode())
txn.put(str(2).encode(), "Bob".encode())
txn.put(str(3).encode(), "Peter".encode())
txn.delete(str(1).encode())
txn.put(str(3).encode(), "Mark".encode())
txn.commit()
```

### 查询

&emsp;&emsp;查单条记录用`get(key)`，遍历数据库用`cursor`：

``` python
import lmdb

env = lmdb.open("students")
txn = env.begin()
print(txn.get(str(2).encode()))
print("------")

for key, value in txn.cursor():
    print(key, value)

env.close()
```

执行结果：

``` bash
b'Bob'
------
b'2' b'Bob'
b'3' b'Mark'
```